<template>
  <div class="day-info-root">

    <!-- 标题 -->
    <div class="info-title-wrapper">
      <bl-row class="info-title">
        <div class="iconbl bl-a-templatelist2-line" style="font-size: 25px;margin-right: 10px;"></div>
        <div>新增计划</div>
      </bl-row>
    </div>

    <div class="info-form">
      <el-form ref="DayFormRef" :model="dayForm" :rules="dayFormRule" label-position="top" label-width="60px">

        <el-form-item label="计划日期" prop="planDate">
          <el-date-picker v-model="dayForm.planDate" type="date" format="YYYY-MM-DD" value-format="YYYY-MM-DD HH:mm:ss"
            style="width: 100%;" />
        </el-form-item>

        <el-form-item label="开始时间 / 结束时间" required>
          <el-form-item prop="planStartTime">
            <el-time-select v-model="dayForm.planStartTime" :max-time="dayForm.planEndTime" placeholder="开始日期"
              start="00:00" step="00:15" end="23:59" style="width: 180px;margin-right: 18px;" />
          </el-form-item>
          <el-form-item prop="planEndTime">
            <el-time-select v-model="dayForm.planEndTime" :min-time="dayForm.planStartTime" placeholder="结束日期"
              start="00:00" step="00:15" end="23:59" style="width: 180px;" />
          </el-form-item>
        </el-form-item>

        <el-form-item>
          <el-checkbox v-model="dayForm.allDay" label="全天" style="margin-right: 50px;" @change="allDayChange" />
          <el-checkbox v-model="dayForm.repeat" label="重复" style="margin-right: 60px;" />
          <div style="margin-right: 10px;">重复天数</div>
          <el-input-number v-model="dayForm.repeatDay" :min="1" :disabled="!dayForm.repeat" />
        </el-form-item>

        <el-form-item label="计划标题" prop="title">
          <el-input v-model="dayForm.title" placeholder="计划标题">
            <template #prefix>
              <el-icon size="15">
                <Document />
              </el-icon>
            </template>
            <template #append>
              <el-tooltip content="查看 Emoji" effect="blossomt" placement="top" :hide-after="0">
                <div style="cursor: pointer;font-size: 15px;" @click="openExtenal('https://www.emojiall.com/zh-hans')">
                  😉
                </div>
              </el-tooltip>
            </template>
          </el-input>
        </el-form-item>

        <el-form-item label="计划内容">
          <el-input type="textarea" :rows="3" v-model="dayForm.content" placeholder="计划内容" />
        </el-form-item>

        <bl-col>
          <bl-row style="font-size: 12px;">
            颜色
            <span class="iconbl bl-a-colorpalette-line" style="font-size: 13px;padding-left: 5px;"></span>
          </bl-row>
          <bl-row class="color-container">
            <bl-row width="50px" height="25px" just="center" v-for="color in colors"
              :class="['color', color, dayForm.color == color ? 'selected' : '']" @click="clickColor(color)">
              <span>{{ color }}</span>
            </bl-row>
          </bl-row>
        </bl-col>
      </el-form>
    </div>

    <div class="info-footer">
      <div></div>
      <el-button size="default" type="primary" @click="saveDay(DayFormRef)">
        <span class="iconbl bl-a-templateadd-line" />保存
      </el-button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue'
import { Document } from '@element-plus/icons-vue'
import type { FormInstance, FormRules } from 'element-plus'
import { planAddDayApi } from '@renderer/api/plan'
import { openExtenal } from '@renderer/assets/utils/electron'

const colors = [
  'purple', 'red', 'yellow', 'blue', 'green', 'gray',
]

interface DayForm {
  title: string, content: string, planDate: string, planStartTime: string, planEndTime: string,
  allDay: boolean, repeat: false, repeatDay: number, color: string
}
const DayFormRef = ref<FormInstance>()
const dayForm = ref<DayForm>({
  title: '',
  content: '',
  planDate: '',
  planStartTime: '',
  planEndTime: '',
  allDay: false,
  repeat: false,
  repeatDay: 0,
  color: 'purple'
})

const dayFormRule = reactive<FormRules<DayForm>>({
  planDate: [
    { required: true, message: '请填写日期', trigger: 'change' }
  ],
  planStartTime: [
    { required: true, message: '请填写开始时间', trigger: 'change' }
  ],
  planEndTime: [
    { required: true, message: '请填写结束时间', trigger: 'change' }
  ],
  title: [
    { required: true, message: '请填写计划标题', trigger: 'change' }
  ]
})

const allDayChange = (allDay: boolean) => {
  if (allDay) {
    dayForm.value.planStartTime = '00:00'
    dayForm.value.planEndTime = '23:59'
  } else {
    dayForm.value.planStartTime = ''
    dayForm.value.planEndTime = ''
  }
}

const clickColor = (color: string) => {
  dayForm.value.color = color
}

const saveDay = async (formEl: FormInstance | undefined) => {
  if (!formEl) return
  await formEl.validate((valid, _fields) => {
    if (valid) {
      console.log(dayForm.value);
      planAddDayApi(dayForm.value).then(_resp => {
        emits('saved')
      })
    }
  })
}

const emits = defineEmits(['saved'])
</script>

<style scoped lang="scss">
// @import url(./PlanColor.scss);

.day-info-root {
  $height-title: 50px;
  $height-footer: 50px;
  $height-form: calc(100% - #{$height-title} - #{$height-footer});

  .info-title-wrapper {
    @include box(100%, $height-title);
    @include flex(row, flex-start, center);
    border-bottom: 1px solid var(--el-border-color);

    .info-title {
      @include font(16px);
      width: calc(100% - 50px - 50px);
      height: 100%;
      color: var(--el-color-primary);
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
      padding-left: 10px;
    }

    .info-title-close {
      width: 50px;
      font-size: 40px;
      color: var(--el-border-color);
      text-align: center;
    }
  }

  .info-form {
    @include box(100%, $height-form);
    padding: 10px;

    :deep(.el-form--inline .el-form-item) {
      margin-right: 20px;
    }
  }

  .info-footer {
    @include box(100%, $height-footer);
    @include flex(row, space-between, center);
    border-top: 1px solid var(--el-border-color);
    padding: 10px;
    text-align: right;

    .iconbl {
      font-size: 18px;
      margin-right: 5px;
    }
  }

  .color-container {
    align-content: flex-start;
    flex-wrap: wrap;
    overflow: scroll;
    overflow-y: overlay;
    padding: 10px;

    .color {
      color: #fff;
      margin: 0 3px;
      border-radius: 5px;
      font-size: 11px;
      cursor: pointer;
      transition: box-shadow 0.2s;
      filter: blur(1px);
    }

    .selected {
      @include themeShadow(0 0 5px 1px #454545, 0 0 8px 1px #000000);
      filter: blur(0);
    }
  }
}
</style>