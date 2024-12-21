<template>
  <view class="visa-application">
    <!-- 签证产品选择部分 -->
    <view class="visa-product-section">
      <text class="section-title">越南电子签证申请</text>
      
      <view class="form-group">
        <text class="label">停留天数</text>
        <radio-group @change="bindDurationChange" class="radio-group">
          <label class="radio-label" v-for="(item, index) in durations" :key="index">
            <radio :value="item" :checked="durationIndex === index" color="#007AFF" />
            <text>{{item}}</text>
          </label>
        </radio-group>
      </view>

      <view class="form-group">
        <text class="label">办理工时</text>
        <radio-group @change="bindProcessingTimeChange" class="radio-group">
          <label class="radio-label" v-for="(item, index) in processingTimes" :key="index">
            <radio :value="item" :checked="processingTimeIndex === index" color="#007AFF" />
            <text>{{item}}</text>
          </label>
        </radio-group>
      </view>

      <view class="form-group">
        <text class="label">入境次数</text>
        <radio-group @change="bindEntryTypeChange" class="radio-group">
          <label class="radio-label" v-for="(item, index) in entryTypes" :key="index">
            <radio :value="item" :checked="entryTypeIndex === index" color="#007AFF" />
            <text>{{item}}</text>
          </label>
        </radio-group>
      </view>

      <view class="form-group">
        <text class="label">办理类型</text>
        <radio-group @change="bindVisaTypeChange" class="radio-group">
          <label class="radio-label" v-for="(item, index) in visaTypes" :key="index">
            <radio :value="item" :checked="visaTypeIndex === index" color="#007AFF" />
            <text>{{item}}</text>
          </label>
        </radio-group>
      </view>
    </view>

    <!-- 护照上传部分 -->
    <view v-if="step >= 2" class="passport-section">
      <text class="section-title">护照信息</text>
      <view class="upload-box" @tap="choosePassportImage">
        <image v-if="visaForm.passportImage" :src="visaForm.passportImage" mode="aspectFit" class="preview-image"></image>
        <text v-else>点击上传护照照片</text>
      </view>

      <!-- 添加客户信息表单 -->
      <text class="section-title">客户信息</text>
      <view class="form-group">
        <text class="label">拼音姓名</text>
        <input type="text" v-model="visaForm.customerInfo.pinyinName" placeholder="请输入拼音姓名" />
      </view>

      <view class="form-group">
        <text class="label">护照号</text>
        <input type="text" v-model="visaForm.customerInfo.passportNo" placeholder="请输入护照号" />
      </view>

      <view class="form-group">
        <text class="label">中文姓名</text>
        <input type="text" v-model="visaForm.customerInfo.chineseName" placeholder="请输入中文姓名" />
      </view>

      <view class="form-group">
        <text class="label">性别</text>
        <radio-group @change="bindGenderChange" class="radio-group">
          <label class="radio-label" v-for="(item, index) in genders" :key="index">
            <radio :value="item" :checked="genderIndex === index" color="#007AFF" />
            <text>{{item}}</text>
          </label>
        </radio-group>
      </view>

      <view class="form-group">
        <text class="label">生日</text>
        <picker mode="date" :value="visaForm.customerInfo.birthday" @change="bindBirthdayChange">
          <view class="picker-value">
            {{visaForm.customerInfo.birthday || '请选择日期'}}
          </view>
        </picker>
      </view>

      <view class="form-group">
        <text class="label">国籍</text>
        <input type="text" v-model="visaForm.customerInfo.nationality" placeholder="请输入国籍" />
      </view>

      <view class="form-group">
        <text class="label">有效期至</text>
        <picker mode="date" :value="visaForm.customerInfo.passportExpiry" @change="bindExpiryChange">
          <view class="picker-value">
            {{visaForm.customerInfo.passportExpiry || '请选择日期'}}
          </view>
        </picker>
      </view>

      <view class="form-group">
        <text class="label">发证日期</text>
        <picker mode="date" :value="visaForm.customerInfo.passportIssueDate" @change="bindIssueDateChange">
          <view class="picker-value">
            {{visaForm.customerInfo.passportIssueDate || '请选择日期'}}
          </view>
        </picker>
      </view>

      <view class="form-group">
        <text class="label">本人电话</text>
        <input type="text" v-model="visaForm.customerInfo.phone" placeholder="请输入电话号码" />
      </view>

      <view class="form-group">
        <text class="label">本人地址</text>
        <input type="text" v-model="visaForm.customerInfo.address" placeholder="请输入地址" />
      </view>
    </view>

    <!-- 照片上传部分 -->
    <view v-if="step >= 3" class="photo-section">
      <text class="section-title">证件照上传</text>
      <view class="upload-box" @tap="choosePhotoImage">
        <image v-if="visaForm.photo" :src="visaForm.photo" mode="aspectFit" class="preview-image"></image>
        <text v-else>点击上传证件照</text>
      </view>
    </view>

    <!-- 联系人信息 -->
    <view v-if="step >= 4" class="contact-section">
      <text class="section-title">紧急联系人信息</text>
      <view class="form-group">
        <text class="label">姓名</text>
        <input type="text" v-model="visaForm.emergencyContact.name" placeholder="请输入联系人姓名" />
      </view>
      <view class="form-group">
        <text class="label">地址</text>
        <input type="text" v-model="visaForm.emergencyContact.address" placeholder="请输入联系人地址" />
      </view>
      <view class="form-group">
        <text class="label">电话</text>
        <input type="number" v-model="visaForm.emergencyContact.phone" placeholder="请输入联系人电话" />
      </view>

      <!-- 商务/工作签证额外信息 -->
      <block v-if="visaForm.visaType !== 'tourist'">
        <text class="section-title">公司信息</text>
        <view class="form-group">
          <text class="label">公司名称</text>
          <input type="text" v-model="visaForm.companyInfo.name" placeholder="请输入公司名称" />
        </view>
        <view class="form-group">
          <text class="label">公司地址</text>
          <input type="text" v-model="visaForm.companyInfo.address" placeholder="请输入公司地址" />
        </view>
        <view class="form-group">
          <text class="label">公司电话</text>
          <input type="number" v-model="visaForm.companyInfo.phone" placeholder="请输入公司电话" />
        </view>
      </block>
    </view>

    <!-- 提交按钮 -->
    <view v-if="step >= 4" class="submit-section">
      <button @tap="submitApplication" :disabled="!isFormValid" type="primary">提交订单</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      step: 1,
      durations: ['90天', '30天'],
      durationIndex: 0,
      processingTimes: ['4小时', '0工', '1工', '2工', '3工', '4工'],
      processingTimeIndex: 0,
      entryTypes: ['单次', '多次'],
      entryTypeIndex: 0,
      visaTypes: ['旅游', '商务', '工作'],
      visaTypeIndex: 0,
      visaForm: {
        duration: '90',
        processingTime: '4',
        entryType: 'single',
        visaType: 'tourist',
        passportImage: '',
        passportInfo: null,
        photo: '',
        emergencyContact: {
          name: '',
          address: '',
          phone: ''
        },
        companyInfo: {
          name: '',
          address: '',
          phone: ''
        },
        customerInfo: {
          pinyinName: '',
          passportNo: '',
          chineseName: '',
          gender: '男',
          birthday: '',
          nationality: '',
          passportExpiry: '',
          passportIssueDate: '',
          phone: '',
          address: ''
        }
      },
      genders: ['男', '女'],
      genderIndex: 0
    }
  },
  computed: {
    isFormValid() {
      const basicInfoValid = this.visaForm.passportInfo && 
                           this.visaForm.photo && 
                           this.visaForm.emergencyContact.name && 
                           this.visaForm.emergencyContact.phone

      if (this.visaForm.visaType === 'tourist') {
        return basicInfoValid
      }

      return basicInfoValid && 
             this.visaForm.companyInfo.name && 
             this.visaForm.companyInfo.phone
    }
  },
  methods: {
    bindDurationChange(e) {
      const selectedValue = e.detail.value
      this.durationIndex = this.durations.indexOf(selectedValue)
      this.visaForm.duration = selectedValue.replace('天', '')
    },
    bindProcessingTimeChange(e) {
      const selectedValue = e.detail.value
      this.processingTimeIndex = this.processingTimes.indexOf(selectedValue)
      this.visaForm.processingTime = selectedValue.replace('工', '').replace('小时', '')
    },
    bindEntryTypeChange(e) {
      const selectedValue = e.detail.value
      this.entryTypeIndex = this.entryTypes.indexOf(selectedValue)
      this.visaForm.entryType = selectedValue === '单次' ? 'single' : 'multiple'
    },
    bindVisaTypeChange(e) {
      const selectedValue = e.detail.value
      this.visaTypeIndex = this.visaTypes.indexOf(selectedValue)
      const types = ['tourist', 'business', 'work']
      this.visaForm.visaType = types[this.visaTypeIndex]
      this.step = 2
    },
    async choosePassportImage() {
      try {
        const res = await uni.chooseImage({
          count: 1,
          sizeType: ['original', 'compressed'],
          sourceType: ['album', 'camera']
        })
        
        this.visaForm.passportImage = res.tempFilePaths[0]
        
        // 这里调用护照识别API
        try {
          // const response = await this.recognizePassport(res.tempFilePaths[0])
          // 模拟护照识别返回数��
          this.visaForm.passportInfo = {
            passportNumber: 'E12345678',
            name: '测试姓名'
          }
          this.step = 3
        } catch (error) {
          uni.showToast({
            title: '护照识别失败',
            icon: 'none'
          })
        }
      } catch (error) {
        console.error('选择图片失败:', error)
      }
    },
    async choosePhotoImage() {
      try {
        const res = await uni.chooseImage({
          count: 1,
          sizeType: ['original', 'compressed'],
          sourceType: ['album', 'camera']
        })
        
        this.visaForm.photo = res.tempFilePaths[0]
        this.step = 4
      } catch (error) {
        console.error('选择图片失败:', error)
      }
    },
    async submitApplication() {
      try {
        // 这里调用提交订单API
        // const response = await this.submitOrder(this.visaForm)
        
        uni.navigateTo({
          url: '/pages/payment/payment?orderId=dummy-order-id'
        })
      } catch (error) {
        uni.showToast({
          title: '提交订单失败',
          icon: 'none'
        })
      }
    },
    bindGenderChange(e) {
      const selectedValue = e.detail.value
      this.genderIndex = this.genders.indexOf(selectedValue)
      this.visaForm.customerInfo.gender = selectedValue
    },
    
    bindBirthdayChange(e) {
      this.visaForm.customerInfo.birthday = e.detail.value
    },
    
    bindExpiryChange(e) {
      this.visaForm.customerInfo.passportExpiry = e.detail.value
    },
    
    bindIssueDateChange(e) {
      this.visaForm.customerInfo.passportIssueDate = e.detail.value
    }
  }
}
</script>

<style>
.visa-application {
  padding: 30rpx 20rpx;
  background-color: #F5F5F5;
  min-height: 100vh;
}

/* 每个部分的卡片样式 */
.visa-product-section,
.passport-section,
.photo-section,
.contact-section {
  background: #FFFFFF;
  border-radius: 16rpx;
  padding: 24rpx 20rpx;
  margin-bottom: 20rpx;
  box-shadow: 0 2rpx 8rpx rgba(0, 0, 0, 0.04);
}

.section-title {
  font-size: 32rpx;
  font-weight: 600;
  color: #333333;
  margin: 20rpx 0 30rpx;
  display: block;
  position: relative;
  padding-left: 20rpx;
}

.section-title::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 6rpx;
  height: 28rpx;
  background: #007AFF;
  border-radius: 3rpx;
}

.form-group {
  margin-bottom: 24rpx;
}

.label {
  display: block;
  margin-bottom: 12rpx;
  font-size: 28rpx;
  color: #333333;
  font-weight: 500;
}

/* 输入框样式优化 */
input {
  width: 100%;
  height: 88rpx;
  padding: 0 24rpx;
  border: 2rpx solid #E5E5E5;
  border-radius: 12rpx;
  box-sizing: border-box;
  font-size: 28rpx;
  background: #FAFAFA;
}

input:focus {
  border-color: #007AFF;
  background: #FFFFFF;
}

/* 上传框样式优化 */
.upload-box {
  width: 100%;
  height: 320rpx;
  border: 2rpx dashed #DDDDDD;
  border-radius: 12rpx;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin: 20rpx 0;
  background: #FAFAFA;
}

.upload-box text {
  font-size: 28rpx;
  color: #666666;
  margin-top: 16rpx;
}

.preview-image {
  width: 100%;
  height: 100%;
  object-fit: contain;
  border-radius: 12rpx;
}

/* 单选按钮组样式优化 */
.radio-group {
  display: flex;
  flex-wrap: wrap;
  gap: 16rpx;
  margin-top: 12rpx;
}

.radio-label {
  flex: 0 0 calc(50% - 8rpx);
  display: flex;
  align-items: center;
  padding: 20rpx 24rpx;
  background-color: #F8F8F8;
  border-radius: 12rpx;
  border: 2rpx solid #E5E5E5;
  box-sizing: border-box;
}

.radio-label.active {
  background-color: #E8F3FF;
  border-color: #007AFF;
}

.radio-label text {
  margin-left: 12rpx;
  font-size: 28rpx;
  color: #333333;
}

/* 日期选择器样式优化 */
.picker-value {
  width: 100%;
  height: 88rpx;
  line-height: 88rpx;
  padding: 0 24rpx;
  border: 2rpx solid #E5E5E5;
  border-radius: 12rpx;
  box-sizing: border-box;
  background: #FAFAFA;
  font-size: 28rpx;
  color: #333333;
}

.picker-value:empty::before {
  content: "请选择日期";
  color: #999999;
}

/* 提交按钮样式优化 */
.submit-section {
  margin: 40rpx 0;
  padding: 0 20rpx;
}

button[type="primary"] {
  width: 100%;
  height: 88rpx;
  line-height: 88rpx;
  background: #007AFF;
  border-radius: 44rpx;
  font-size: 32rpx;
  font-weight: 500;
  border: none;
}

button[disabled] {
  background-color: #CCCCCC !important;
  color: #FFFFFF !important;
  opacity: 0.7;
}

/* 添加安全区域适配 */
@supports (padding-bottom: constant(safe-area-inset-bottom)) {
  .submit-section {
    padding-bottom: constant(safe-area-inset-bottom);
  }
}

@supports (padding-bottom: env(safe-area-inset-bottom)) {
  .submit-section {
    padding-bottom: env(safe-area-inset-bottom);
  }
}

/* 适配暗黑模式 */
@media (prefers-color-scheme: dark) {
  .visa-application {
    background-color: #1A1A1A;
  }

  .visa-product-section,
  .passport-section,
  .photo-section,
  .contact-section {
    background: #2C2C2C;
  }

  .section-title,
  .label,
  input,
  .radio-label text {
    color: #FFFFFF;
  }

  input,
  .picker-value {
    background: #333333;
    border-color: #444444;
  }

  .radio-label {
    background-color: #333333;
    border-color: #444444;
  }

  .radio-label.active {
    background-color: #0D3A66;
    border-color: #007AFF;
  }
}
</style> 