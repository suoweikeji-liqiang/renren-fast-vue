<template>
  <div class="mod-demo-echarts">
    <el-select v-model="value1" clearable placeholder="请选择客户">
      <el-option
        v-for="item in options"
        :key="item.value"
        :label="item.label"
        :value="item.value">
      </el-option>
    </el-select>

    <el-select v-model="value1" clearable placeholder="请选择楼宇">
      <el-option
        v-for="item in options"
        :key="item.value"
        :label="item.label"
        :value="item.value">
      </el-option>
    </el-select>

   <div class="sfznav divc" style="margin-top: 20px; overflow: hidden; border: 10; padding: 0px; margin: 0px;height:1200px;">
	<iframe id="myframe" style="left: -60px; top: 40px; position: relative;" name="myframe" src="http://118.24.162.152:8089/d/1jk49o0ik/ce-shi?refresh=5s&orgId=1&from=now-1h&to=now"  frameborder="0"  scrolling="no"  width="100%" height="100%">
	</iframe>
	
</div>

  </div>

</template>

