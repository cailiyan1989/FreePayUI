<!--
  -    Copyright (c) 2018-2025, lengleng All rights reserved.
  -
  - Redistribution and use in source and binary forms, with or without
  - modification, are permitted provided that the following conditions are met:
  -
  - Redistributions of source code must retain the above copyright notice,
  - this list of conditions and the following disclaimer.
  - Redistributions in binary form must reproduce the above copyright
  - notice, this list of conditions and the following disclaimer in the
  - documentation and/or other materials provided with the distribution.
  - Neither the name of the pig4cloud.com developer nor the names of its
  - contributors may be used to endorse or promote products derived from
  - this software without specific prior written permission.
  - Author: lengleng (wangiegie@gmail.com)
  -->
<template>
  <div class="execution">
    <basic-container>
      <avue-crud
        ref="crud"
        :page="page"
        :data="tableData"
        :permission="permissionList"
        :table-loading="tableLoading"
        :option="tableOption"
        @on-load="getList"
        @search-change="searchChange"
        @refresh-change="refreshChange"
        @size-change="sizeChange"
        @current-change="currentChange">
      </avue-crud>
    </basic-container>
  </div>
</template>

<script>
import {
  fetchList
} from '@/api/pay/paytradeorder'
import {
  tableOption
} from '@/const/crud/pay/paytradeorder'
import {
  mapGetters
} from 'vuex'

export default {
  name: 'Paytradeorder',
  data() {
    return {
      searchForm: {},
      tableData: [],
      page: {
        total: 0, // 总页数
        currentPage: 1, // 当前页数
        pageSize: 20 // 每页显示多少条
      },
      tableLoading: false,
      tableOption: tableOption
    }
  },
  computed: {
    ...mapGetters(['permissions']),
    permissionList() {
      return {
        addBtn: this.vaildData(this.permissions.generator_paytradeorder_add, false),
        delBtn: this.vaildData(this.permissions.generator_paytradeorder_del, false),
        editBtn: this.vaildData(this.permissions.generator_paytradeorder_edit, false)
      }
    }
  },
  methods: {
    getList(page, params) {
      this.tableLoading = true
      fetchList(Object.assign({
        descs: 'create_time',
        current: page.currentPage,
        size: page.pageSize
      }, params, this.searchForm)).then(response => {
        this.tableData = response.data.data.records
        this.page.total = response.data.data.total
        this.tableLoading = false
      }).catch(() => {
        this.tableLoading = false
      })
    },
    searchChange(form, done) {
      this.searchForm = form
      this.page.currentPage = 1
      this.getList(this.page, form)
      done()
    },
    sizeChange(pageSize){
      this.page.pageSize = pageSize
    },
    currentChange(current){
      this.page.currentPage = current
    },
    refreshChange() {
      this.getList(this.page)
    },
  }
}
</script>
