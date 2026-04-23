<template>
  <div class="settings-container">
    <Navbar activeMenu="settings" />
    
    <div class="main-content">
      <!-- 页面标题 -->
      <div class="page-header">
        <h1>设置</h1>
        <p>管理您的账户和系统设置</p>
      </div>
      
      <!-- 设置选项卡 -->
      <div class="settings-tabs">
        <button 
          v-for="tab in tabs" 
          :key="tab.id"
          :class="['tab-button', { active: activeTab === tab.id }]"
          @click="activeTab = tab.id"
        >
          {{ tab.name }}
        </button>
      </div>
      
      <!-- 设置内容 -->
      <div class="settings-content">
        <!-- 个人信息设置 -->
        <div v-if="activeTab === 'profile'" class="settings-section">
          <h2>个人信息</h2>
          <div class="settings-form">
            <div class="form-item">
              <label>用户名</label>
              <input type="text" v-model="profileForm.username" placeholder="请输入用户名">
            </div>
            <div class="form-item">
              <label>邮箱</label>
              <input type="email" v-model="profileForm.email" placeholder="请输入邮箱">
            </div>
            <div class="form-item">
              <label>手机号码</label>
              <input type="tel" v-model="profileForm.phone" placeholder="请输入手机号码">
            </div>
            <div class="form-item">
              <label>头像</label>
              <div class="avatar-upload">
                <div class="avatar-preview">
                  <img :src="profileForm.avatar" alt="头像" v-if="profileForm.avatar">
                  <div class="avatar-placeholder" v-else>
                    <span>上传头像</span>
                  </div>
                </div>
                <input type="file" @change="handleAvatarUpload" accept="image/*" ref="avatarInput">
              </div>
            </div>
            <div class="form-actions">
              <button class="cancel-btn" @click="resetProfileForm">取消</button>
              <button class="submit-btn" @click="saveProfile">保存</button>
            </div>
          </div>
        </div>
        
        <!-- 密码修改设置 -->
        <div v-if="activeTab === 'password'" class="settings-section">
          <h2>密码修改</h2>
          <div class="settings-form">
            <div class="form-item">
              <label>当前密码</label>
              <input type="password" v-model="passwordForm.oldPassword" placeholder="请输入当前密码">
            </div>
            <div class="form-item">
              <label>新密码</label>
              <input type="password" v-model="passwordForm.newPassword" placeholder="请输入新密码">
            </div>
            <div class="form-item">
              <label>确认新密码</label>
              <input type="password" v-model="passwordForm.confirmPassword" placeholder="请确认新密码">
            </div>
            <div class="form-actions">
              <button class="cancel-btn" @click="resetPasswordForm">取消</button>
              <button class="submit-btn" @click="changePassword">修改密码</button>
            </div>
          </div>
        </div>
        
        <!-- 系统设置 -->
        <div v-if="activeTab === 'system'" class="settings-section">
          <h2>系统设置</h2>
          <div class="settings-form">
            <div class="form-item">
              <label>语言</label>
              <select v-model="systemForm.language" class="select-input">
                <option value="zh-CN">简体中文</option>
                <option value="en-US">English</option>
              </select>
            </div>
            <div class="form-item">
              <label>主题</label>
              <div class="theme-options">
                <div 
                  v-for="theme in themes" 
                  :key="theme.value"
                  :class="['theme-option', { active: systemForm.theme === theme.value }]"
                  @click="systemForm.theme = theme.value"
                >
                  <div class="theme-color" :style="{ backgroundColor: theme.color }"></div>
                  <span>{{ theme.name }}</span>
                </div>
              </div>
            </div>
            <div class="form-item checkbox-item">
              <input type="checkbox" id="notifications" v-model="systemForm.notifications">
              <label for="notifications">接收系统通知</label>
            </div>
            <div class="form-item checkbox-item">
              <input type="checkbox" id="autoSave" v-model="systemForm.autoSave">
              <label for="autoSave">自动保存设置</label>
            </div>
            <div class="form-actions">
              <button class="cancel-btn" @click="resetSystemForm">取消</button>
              <button class="submit-btn" @click="saveSystemSettings">保存</button>
            </div>
          </div>
        </div>
        
        <!-- 关于 -->
        <div v-if="activeTab === 'about'" class="settings-section">
          <h2>关于</h2>
          <div class="about-content">
            <div class="about-item">
              <label>版本</label>
              <span>1.0.0</span>
            </div>
            <div class="about-item">
              <label>开发者</label>
              <span>活动管理系统团队</span>
            </div>
            <div class="about-item">
              <label>更新日期</label>
              <span>2026-04-23</span>
            </div>
            <div class="about-item">
              <label>版权信息</label>
              <span>© 2026 活动管理系统</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import Navbar from './Navbar.vue';

// 选项卡
const tabs = [
  { id: 'profile', name: '个人信息' },
  { id: 'password', name: '密码修改' },
  { id: 'system', name: '系统设置' },
  { id: 'about', name: '关于' }
];

const activeTab = ref('profile');

// 个人信息表单
const profileForm = ref({
  username: '',
  email: '',
  phone: '',
  avatar: ''
});

// 密码修改表单
const passwordForm = ref({
  oldPassword: '',
  newPassword: '',
  confirmPassword: ''
});

// 系统设置表单
const systemForm = ref({
  language: 'zh-CN',
  theme: 'blue',
  notifications: true,
  autoSave: true
});

// 主题选项
const themes = [
  { value: 'blue', name: '蓝色', color: '#6b93c7' },
  { value: 'green', name: '绿色', color: '#4caf50' },
  { value: 'purple', name: '紫色', color: '#9c27b0' },
  { value: 'orange', name: '橙色', color: '#ff9800' }
];

// 加载用户信息
const loadUserInfo = () => {
  const user = localStorage.getItem('user');
  if (user) {
    try {
      const userObj = JSON.parse(user);
      profileForm.value.username = userObj.username || '';
      profileForm.value.email = userObj.email || '';
      profileForm.value.phone = userObj.phone || '';
      profileForm.value.avatar = userObj.avatar || '';
    } catch (error) {
      console.error('解析用户信息失败:', error);
    }
  }
};

// 加载系统设置
const loadSystemSettings = () => {
  const settings = localStorage.getItem('systemSettings');
  if (settings) {
    try {
      const settingsObj = JSON.parse(settings);
      systemForm.value = { ...systemForm.value, ...settingsObj };
    } catch (error) {
      console.error('解析系统设置失败:', error);
    }
  }
};

// 处理头像上传
const handleAvatarUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      profileForm.value.avatar = e.target.result;
    };
    reader.readAsDataURL(file);
  }
};

// 保存个人信息
const saveProfile = () => {
  // 模拟保存
  const user = localStorage.getItem('user');
  let userObj = {};
  if (user) {
    try {
      userObj = JSON.parse(user);
    } catch (error) {
      console.error('解析用户信息失败:', error);
    }
  }
  
  userObj = {
    ...userObj,
    username: profileForm.value.username,
    email: profileForm.value.email,
    phone: profileForm.value.phone,
    avatar: profileForm.value.avatar
  };
  
  localStorage.setItem('user', JSON.stringify(userObj));
  alert('个人信息保存成功！');
};

// 重置个人信息表单
const resetProfileForm = () => {
  loadUserInfo();
};

// 修改密码
const changePassword = () => {
  if (!passwordForm.value.oldPassword) {
    alert('请输入当前密码');
    return;
  }
  
  if (!passwordForm.value.newPassword) {
    alert('请输入新密码');
    return;
  }
  
  if (passwordForm.value.newPassword !== passwordForm.value.confirmPassword) {
    alert('两次输入的密码不一致');
    return;
  }
  
  // 模拟密码修改
  alert('密码修改成功！');
  resetPasswordForm();
};

// 重置密码表单
const resetPasswordForm = () => {
  passwordForm.value = {
    oldPassword: '',
    newPassword: '',
    confirmPassword: ''
  };
};

// 保存系统设置
const saveSystemSettings = () => {
  localStorage.setItem('systemSettings', JSON.stringify(systemForm.value));
  alert('系统设置保存成功！');
};

// 重置系统设置
const resetSystemForm = () => {
  loadSystemSettings();
};

// 组件挂载时的初始化
onMounted(() => {
  loadUserInfo();
  loadSystemSettings();
});
</script>

<style scoped>
.settings-container {
  display: flex;
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  background-attachment: fixed;
}

.main-content {
  flex: 1;
  margin-left: 260px;
  padding: 40px;
  transition: margin-left 0.3s ease;
  min-height: 100vh;
  background-color: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
}

/* 页面标题 */
.page-header {
  text-align: center;
  margin-bottom: 40px;
  padding: 30px 0;
  background: white;
  border-radius: 16px;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.1);
}

.page-header h1 {
  margin: 0 0 10px 0;
  font-size: 32px;
  font-weight: 700;
  color: #333333;
}

.page-header p {
  margin: 0;
  font-size: 16px;
  color: #666666;
}

/* 设置选项卡 */
.settings-tabs {
  display: flex;
  gap: 10px;
  margin-bottom: 30px;
  background: white;
  padding: 10px;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.tab-button {
  padding: 12px 24px;
  border: none;
  border-radius: 8px;
  background: #f8f9fa;
  color: #333333;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  flex: 1;
  text-align: center;
}

.tab-button:hover {
  background: #e9ecef;
  transform: translateY(-2px);
}

.tab-button.active {
  background: #6b93c7;
  color: white;
  box-shadow: 0 4px 12px rgba(107, 147, 199, 0.3);
}

/* 设置内容 */
.settings-content {
  background: white;
  border-radius: 16px;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.1);
  padding: 30px;
  min-height: 500px;
}

/* 设置 section */
.settings-section {
  margin-bottom: 30px;
}

.settings-section h2 {
  margin: 0 0 20px 0;
  font-size: 20px;
  font-weight: 600;
  color: #333333;
  padding-bottom: 10px;
  border-bottom: 1px solid #e9ecef;
}

/* 设置表单 */
.settings-form {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.form-item {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.form-item label {
  font-weight: 500;
  color: #333333;
  font-size: 14px;
}

.form-item input {
  padding: 10px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 14px;
  outline: none;
  transition: border-color 0.3s ease;
}

.form-item input:focus {
  border-color: #6b93c7;
  box-shadow: 0 0 0 2px rgba(107, 147, 199, 0.1);
}

/* 头像上传 */
.avatar-upload {
  position: relative;
  width: 120px;
  height: 120px;
  cursor: pointer;
}

.avatar-preview {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  overflow: hidden;
  border: 2px dashed #ddd;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.avatar-preview:hover {
  border-color: #6b93c7;
  background-color: #f8f9fa;
}

.avatar-preview img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.avatar-placeholder {
  text-align: center;
  color: #666666;
  font-size: 14px;
}

.avatar-upload input[type="file"] {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
  cursor: pointer;
}

/* 选择框 */
.select-input {
  padding: 10px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 14px;
  outline: none;
  transition: border-color 0.3s ease;
  cursor: pointer;
}

.select-input:focus {
  border-color: #6b93c7;
  box-shadow: 0 0 0 2px rgba(107, 147, 199, 0.1);
}

/* 主题选项 */
.theme-options {
  display: flex;
  gap: 15px;
  flex-wrap: wrap;
}

.theme-option {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  padding: 10px;
  border-radius: 8px;
  transition: all 0.3s ease;
  width: 80px;
}

.theme-option:hover {
  background-color: #f8f9fa;
}

.theme-option.active {
  background-color: #e9ecef;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.theme-color {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 2px solid #ddd;
}

.theme-option span {
  font-size: 12px;
  color: #333333;
  font-weight: 500;
}

/* 复选框项 */
.checkbox-item {
  flex-direction: row;
  align-items: center;
  gap: 10px;
}

.checkbox-item input[type="checkbox"] {
  width: auto;
  height: auto;
  cursor: pointer;
}

.checkbox-item label {
  margin: 0;
  cursor: pointer;
}

/* 表单操作按钮 */
.form-actions {
  display: flex;
  justify-content: flex-end;
  gap: 15px;
  margin-top: 20px;
}

.cancel-btn {
  padding: 10px 24px;
  background-color: #f8f9fa;
  color: #333333;
  border: 1px solid #dee2e6;
  border-radius: 6px;
  font-size: 14px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.cancel-btn:hover {
  background-color: #e9ecef;
  border-color: #ced4da;
}

.submit-btn {
  padding: 10px 24px;
  background-color: #6b93c7;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.submit-btn:hover {
  background-color: #5a7fb8;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(107, 147, 199, 0.3);
}

/* 关于内容 */
.about-content {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.about-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  background-color: #f8f9fa;
  border-radius: 8px;
}

.about-item label {
  font-weight: 600;
  color: #333333;
  font-size: 14px;
}

.about-item span {
  color: #666666;
  font-size: 14px;
}

/* 响应式 */
@media (max-width: 768px) {
  .main-content {
    margin-left: 70px;
    padding: 20px;
  }
  
  .page-header h1 {
    font-size: 24px;
  }
  
  .settings-content {
    padding: 20px;
  }
  
  .settings-tabs {
    flex-direction: column;
  }
  
  .tab-button {
    padding: 10px 16px;
  }
  
  .theme-options {
    justify-content: center;
  }
  
  .about-item {
    flex-direction: column;
    align-items: flex-start;
    gap: 5px;
  }
  
  .form-actions {
    flex-direction: column;
  }
  
  .cancel-btn,
  .submit-btn {
    width: 100%;
  }
}
</style>