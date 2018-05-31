<template>
  <div class="pageHome">
    <div class="homeTreeBox">
      <my-tree
        :isSiblings='true'
        :isCheckBox='false'
        :tree-list='treeData'
        :callback='callback'>
      </my-tree>
    </div>
  </div>
</template>

<script>
import myTree from '@/components/tree'
export default {
  data () {
    return {
      treeData: [{
        id: 0,
        clickNode: false,
        isFolder: false,
        num: 999,
        parentId: -1,
        parentName: '',
        userId: 101,
        userName: 'A级用户1',
        children: [
          {
            id: 1,
            clickNode: false,
            isFolder: false,
            num: 666,
            parentId: 101,
            parentName: 'A级用户1',
            userId: 201,
            userName: 'B级用户1',
            children: [
              {
                id: 11,
                clickNode: false,
                isFolder: false,
                num: 100,
                parentId: 201,
                parentName: 'B级用户2',
                userId: 311,
                userName: 'C级用户11',
                children: []
              }
            ]
          },
          {
            id: 2,
            clickNode: false,
            isFolder: false,
            num: 555,
            parentId: 101,
            parentName: 'A级用户1',
            userId: 202,
            userName: 'B级用户2',
            children: [
              {
                id: 3,
                clickNode: false,
                isFolder: false,
                num: 100,
                parentId: 202,
                parentName: 'B级用户2',
                userId: 301,
                userName: 'C级用户1',
                children: []
              }
            ]
          }
        ]
      },{
        id: 4,
        clickNode: false,
        isFolder: false,
        num: 888,
        parentId: -1,
        parentName: '',
        userId: 102,
        userName: 'A级用户2',
        children: [
          {
            id: 5,
            clickNode: false,
            isFolder: false,
            num: 1000,
            parentId: 102,
            parentName: 'A级用户2',
            userId: 203,
            userName: 'B级用户3',
            children: []
          },
          {
            id: 6,
            clickNode: false,
            isFolder: false,
            num: 1000,
            parentId: 102,
            parentName: 'A级用户2',
            userId: 204,
            userName: 'B级用户4',
            children: [
              {
                id: 7,
                clickNode: false,
                isFolder: false,
                num: 100,
                parentId: 202,
                parentName: 'B级用户4',
                userId: 302,
                userName: 'C级用户2',
                children: []
              },
              {
                id: 8,
                clickNode: false,
                isFolder: false,
                num: 100,
                parentId: 202,
                parentName: 'B级用户4',
                userId: 303,
                userName: 'C级用户3',
                children: []
              },
              {
                id: 9,
                clickNode: false,
                isFolder: false,
                num: 100,
                parentId: 202,
                parentName: 'B级用户4',
                userId: 304,
                userName: 'C级用户4',
                children: []
              },
              {
                id: 10,
                clickNode: false,
                isFolder: false,
                num: 100,
                parentId: 202,
                parentName: 'B级用户4',
                userId: 305,
                userName: 'C级用户5',
                children: []
              }
            ]
          }
        ]
      }]
    }
  },
  methods: {
    /**
     * tree 事件回调
     * 参数： thisNode(当前节点对象)， treeData(所有节点对象)，type('click':单击事件，'dbclick':双击事件，'open':节点展开关闭事件)
     */
    callback (thisNode, treeData, type) {
      console.log(type)
    }

  },
  components: {
    myTree
  }
}
</script>

<style scoped>
  .pageHome{
    width: 100%;
    height: 100%;
  }
  .homeTreeBox{
    width: 300px;
    height: 500px;
    border: 1px solid red;
  }
</style>
