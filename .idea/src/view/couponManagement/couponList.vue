<template>
  <div class='couponList'>
    <Breadcrumb>
      <BreadcrumbItem>优惠券管理</BreadcrumbItem>
      <BreadcrumbItem>优惠券列表</BreadcrumbItem>
    </Breadcrumb>

    <div class="topBox1">
      <i-button type="primary" @click="modal1 = true">新增优惠券</i-button>
      <Modal
        :visible.sync="modal1"
        title="普通的Modal对话框标题"
        @on-ok="ok"
        @on-cancel="cancel">
        <p>对话框内容</p>
        <p>对话框内容</p>
        <p>对话框内容</p>
      </Modal>
      <Dropdown style="margin-left: 20px;margin-top: 15px" @on-click="operation">
        <Button type="ghost">
          批量操作
          <Icon type="arrow-down-b"></Icon>
        </Button>
        <DropdownMenu slot="list">
          <DropdownItem name="1">上架</DropdownItem>
          <DropdownItem name="2">下架</DropdownItem>
          <DropdownItem name="3">删除</DropdownItem>
        </DropdownMenu>
      </Dropdown>
    </div>





  </div>
</template>

<script>
  export default {
    data() {
      return {
        modal1: false
      }
    },
    methods: {
      ok() {
        this.$Message.info('点击了确定')
      },
      cancel() {
        this.$Message.info('点击了取消')
      }
    }
  }
</script>

<style scoped>
  .couponList{

  }
  .topBox1 {
    display: block;
    width: 100%;
    height: 120px;
    margin: 12px 0;
    padding: 15px;
    border-radius: 6px;
    border: 1px solid #eeeeee;
  }
</style>
