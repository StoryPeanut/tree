<template>
  <div class="tree_warp">
    <ul class="tree_ul">
      <tree-item
        root='0'
        v-for="(item, index) in treeData"
        :key="index"
        :tree-data.sync='treeData'
        :this-node.sync='item'
        :this-index.sync='index'
        :tree-len.sync='treeData.length'
        :callback='callback'
        :is-check-box.sync='isCheckBox'
        :is-siblings.sync='isSiblings'>
      </tree-item>
    </ul>
  </div>
</template>

<script>
// 检查是否为数组
function isArrayFn(value){
  if (typeof Array.isArray === "function") {
    return Array.isArray(value);
  } else {
    return Object.prototype.toString.call(value) === "[object Array]";
  }
}

export default {
  data () {
    return {
      treeData: []
    }
  },
  props: {
    treeList: { // tree 数据
      type: Array,
      twoWay: true
    },
    callback: { // 回调
      type: Function,
      default: null
    },
    isCheckBox: {
      type: Boolean, // 是否可以多选
      default: false
    },
    isSiblings: {
      type: Boolean, // 是否保持单一节点打开
      default: false
    }
  },
  watch: {
    treeList: {
      handler: function(val) {
        this.initTreeData();
      },
      deep: true
    }
  },
  methods: {
    /**
     * @function
     * 初始化tree数据结构
     */
    initTreeData () {
      let treeData = JSON.parse(JSON.stringify(this.treeList))
      let recursion = (data) => {
        data.forEach(info => {
          info.clickNode = info.clickNode || false  // 是否选中节点
          info.isFolder = info.isFolder || false    // 是否打开节点
          info.children = info.children || []       // 下级节点数组
          if (info.children) {
            recursion(info.children)
          }
        })
      }

      recursion(treeData)

      // 根节点第一个列表展开
      if(treeData.length != 0){
        treeData[0].isFolder = true
      }

      this.treeData = treeData
    }

  },
  mounted () {
    this.initTreeData();
  },
  components: {
    treeItem: {
      name: 'treeItem',
      data () {
        return {
          parentNode: null  // 当前点击节点的父节点对象
        }
      },
      props: {
        root: {
          type: String,
          twoWay: true
        },
        treeData: {
          type: Array,
          twoWay: true,
          default: []
        },
        thisNode: {
          type: Object,
          twoWay: true
        },
        thisIndex: {
          type: Number,
          twoWay: true
        },
        treeLen: {
          type: Number,
          twoWay: true,
          default: 0
        },
        callback: {
          type: Function
        },
        isCheckBox: {
          type: Boolean,
          default: false
        },
        isSiblings: {
          type: Boolean,
          defalut: false
        }
      },
      methods: {
        /**
         * @click
         * 节点单击事件， 节点双击事件，节点打开、关闭事件
         * 参数：thisNode(当前点击节点对象)，index(当前点击节点下标)
         * 参数：type='click'(单击)，='dbclick'(双击)，='open'(打开、关闭)
         */
        onClick (thisNode, index, type) {
          // 递归操作，查询点击的节点
          let recursion = (data, list) => { // list 为当前节点的父节点对象
            data.forEach(info => {
              if (info.id == thisNode.id) {
                if (type == 'click') {
                  info.clickNode = !info.clickNode
                } else if (type == 'open') {
                  info.isFolder = !info.isFolder
                }
                if (type == 'click' && !thisNode.isFolder && info.clickNode) {
                  info.isFolder = true
                }
                if (typeof this.callback == "function") {
                  this.callback.call(null, thisNode, this.treeData, type)
                }
                if (this.isSiblings) { // 如果要保持单一节点打开
                  for (let i = 0; i < list.length; i++) {
                    if (list[i].userId != info.userId) {
                      let l = list[i]
                      this.$set(l, 'isFolder', false )
                    }
                  }
                }
              } else if (!this.isCheckBox){
                info.clickNode = false
              }

              if (info.children) {
                recursion(info.children, info.children);
              }
            })
          }

          recursion(this.treeData, this.treeData)
        }
      },
      computed: {
        // 给（根 和 子树）赋值不同的样式
        rootClass() {
          var strRootClass = "";
          // 根判断
          if (this.root == "0") {
            this.thisNode.children = this.thisNode.children || [];
            strRootClass = this.thisIndex == 0 && this.thisNode.children.length == 0
              ? "roots_docu"
              : this.treeLen == 1 || (this.thisIndex == 0 && this.treeLen != this.thisIndex + 1)
              ? "root_"
              : this.treeLen == this.thisIndex + 1
              ? "bottom_" : "center_"
          // 子树判断
          } else if (this.root == "1") {
            this.thisNode.children = this.thisNode.children || []
            strRootClass = this.treeLen > 1 && this.thisNode.children.length > 0 && this.treeLen != this.thisIndex + 1
              ? "center_"
              : (this.thisIndex == 0 && this.treeLen > 1) || this.treeLen != this.thisIndex + 1
              ? "center_docu"
              : (this.treeLen == 1 && this.thisIndex != 0) || (this.treeLen == this.thisIndex + 1 && this.thisNode.children.length > 0)
              ? "bottom_"
              : "bottom_docu"
          }
          return strRootClass
        },
        // 是否有儿子节点
        isChildren() {
          return this.thisIndex + 1 != this.treeLen;
        },
        // 展开/收起
        prefixClass() {
          var returnChar = "";
          if (this.rootClass.indexOf("docu") == -1) {
            if (this.thisNode.isFolder) {
              returnChar = "open";
            } else {
              returnChar = "close";
            }
          }

          if (
            this.thisNode.children.length == 0 && this.rootClass.indexOf("docu") == -1
          ) {
            returnChar = "docu";
          }

          return returnChar;
        },
        liClassVal() {
          return "level" + this.thisIndex;
        },
        spanClassVal() {
          return ( "button level" + this.thisIndex + " switch " + this.rootClass + this.prefixClass
          );
        },

        aClassVal() {
          return this.thisNode.clickNode
            ? "level" + this.thisIndex + " curSelectedNode"
            : "level" + this.thisIndex;
        },
        aIdVal () {
          return `level${this.thisNode.userId}`
        },
        ulClassVal() {
          return this.isChildren && this.thisNode.children.length > 0
            ? "level" + this.thisIndex + " line"
            : "level" + this.thisIndex;
        }
      },
      template: 
      `<li>
        <div class="listTitle">
          <span :class="spanClassVal" @click='onClick(thisNode, thisIndex, "open")'></span>
          <a :id='aIdVal' :class='aClassVal' @click='onClick(thisNode, thisIndex, "click")' @dblclick='onClick(thisNode, thisIndex, "dbclick")'>
            <span class="tree_icon"></span>
            <span v-text="thisNode.userName"></span>
          </a>
        </div>
        <ul :class="ulClassVal" v-show='thisNode.isFolder'>
          <tree-item
            root='1'
            v-for="(item,index) in thisNode.children" 
            :key='index' 
            :tree-data.sync='treeData'
            :this-node.sync="item"
            :this-index.sync='index'
            :callback='callback'
            :tree-len.sync='thisNode.children.length'
            :is-check-box.sync='isCheckBox'
            :is-siblings.sync='isSiblings'>
          </tree-item>
        </ul>
      </li>`
    }
  }
}
</script>

<style >
  .tree_warp * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-size: 12px;
    font-family: Verdana, Arial, Helvetica, AppleGothic, sans-serif;
  }
  .tree_warp{
    width: 100%;
    text-align: left;
  }
  .tree_warp .tree_ul{
    background: #fff;
    width: 100%;
    height: auto;
    padding: 5px;
  }
  .listTitle{
    display: flex;
    justify-content: flex-start;
    align-items: center;
    margin-bottom: 2px;
  }
  .tree_ul li {
    position: relative;
    list-style: none;
    text-align: left;
    white-space: nowrap;
    outline: 0;
  }
  .tree_ul li ul {
    padding: 0 0 0 18px;
  }
  .tree_ul li ul.line {
    background: url('~@/assets/imgs/line_conn.gif') 0 0 repeat-y;
  }
  .tree_ul li a {
    cursor: pointer;
    color: #333;
    background-color: transparent;
    text-decoration: none;
    height: 20px;
    padding: 0 5px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .tree_ul li a .tree_icon{
    background: url('~@/assets/imgs/sitemap.png') 0 0 no-repeat;
    width: 16px;
    height: 16px;
    margin-right: 3px;
  }
  .tree_ul li a:hover {
    text-decoration: underline;
  }

  .tree_ul li span.button {
    cursor: pointer;
    background-color: transparent;
    background-repeat: no-repeat;
    background-attachment: scroll;
    background-image: url("~@/assets/imgs/zTreeStandard.png");
  }
  .tree_ul li a.curSelectedNode {
    text-decoration: none;
    opacity: 0.8;
    background: #0070ff;
    border: 1px solid #0070ff;
    color: #fff;
  }

  .tree_ul li span.button.switch {
    width: 18px;
    height: 18px;
  }
  .tree_ul li span.button.root_open {
    background-position: -92px -54px;
  }
  .tree_ul li span.button.root_close {
    background-position: -74px -54px;
  }
  .tree_ul li span.button.roots_open {
    background-position: -92px 0;
  }
  .tree_ul li span.button.roots_close {
    background-position: -74px 0;
  }
  .tree_ul li span.button.center_open {
    background-position: -92px -18px;
  }
  .tree_ul li span.button.center_close {
    background-position: -74px -18px;
  }
  .tree_ul li span.button.bottom_open {
    background-position: -92px -36px;
  }
  .tree_ul li span.button.bottom_close {
    background-position: -74px -36px;
  }
  .tree_ul li span.button.noline_open {
    background-position: -92px -72px;
  }
  .tree_ul li span.button.noline_close {
    background-position: -74px -72px;
  }
  .tree_ul li span.button.root_docu {
    background: none;
  }
  .tree_ul li span.button.roots_docu {
    background-position: -56px 0;
  }
  .tree_ul li span.button.center_docu {
    background-position: -56px -18px;
  }
  .tree_ul li span.button.bottom_docu {
    background-position: -56px -36px;
  }
  .tree_ul li span.button.noline_docu {
    background: none;
  }
  
</style>
