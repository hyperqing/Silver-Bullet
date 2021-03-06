<template>
    <div class="container clearfix">
        <el-row :gutter="20">
            <el-col :span="4">
                <div class="avatar-box">
                    <img :src="profileData.user_avatar" alt="用户头像" class="avatar">
                    <el-button type="text" native-type="button" @click="updateAvatarData.isVisible = true">
                        修改头像
                    </el-button>
                </div>
            </el-col>
            <el-col :span="10">
                <el-form label-position="top" label-width="80px" :model="form">
                    <el-form-item label="姓名">
                        <el-input v-model="form.user_name" @blur="updateName"></el-input>
                    </el-form-item>
                    <el-form-item label="密码">
                        <el-input v-model="profileData.user_password" disabled></el-input>
                        <el-button type="text" native-type="button" @click="updatePasswordData.isVisible=true">修改密码
                        </el-button>
                    </el-form-item>
                </el-form>
            </el-col>
            <el-col :span="10">
                <el-form label-position="top" label-width="80px" :model="form">
                    <el-form-item label="邮箱">
                        <el-input v-model="form.email" disabled></el-input>
                    </el-form-item>
                    <el-form-item label="职位">
                        <el-input v-model="form.job" @blur="updateJob"></el-input>
                    </el-form-item>
                </el-form>
            </el-col>
        </el-row>
        <el-dialog title="修改密码" :visible.sync="updatePasswordData.isVisible" width="30%">
            <el-form label-width="80px" :model="form">
                <el-form-item label="原密码">
                    <el-input type="password" v-model="updatePasswordData.original_password"></el-input>
                </el-form-item>
                <el-form-item label="新密码">
                    <el-input type="password" v-model="updatePasswordData.new_password"></el-input>
                </el-form-item>
                <el-form-item label="确认密码">
                    <el-input type="password" v-model="updatePasswordData.confirm_password"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="updatePasswordData.isVisible = false">取 消</el-button>
                <el-button type="primary" @click="updatePassword">确 定</el-button>
              </span>
        </el-dialog>
        <el-dialog title="修改头像" :visible.sync="updateAvatarData.isVisible" width="30%">
            <span>请选择头像</span>
            <el-upload class="avatar-uploader"
                       :action="profileData.updateAvatarUrl"
                       :show-file-list="false" name="avatar"
                       :on-success="handleAvatarSuccess">
                <img v-if="updateAvatarData.imageUrl" :src="updateAvatarData.imageUrl" class="avatar">
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
            <span slot="footer" class="dialog-footer">
                <el-button @click="updateAvatarData.isVisible = false">取 消</el-button>
                <el-button type="primary" @click="updateAvatarData.isVisible = false">完 成</el-button>
              </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        name: "profile-item",
        data() {
            return {
                form: {
                    user_name: this.profileData.user_name,
                    user_avatar: this.profileData.user_avatar,
                    email: this.profileData.email,
                    job: this.profileData.job,
                },
                updatePasswordData: {
                    isVisible: false, // 修改密码弹窗
                    original_password: "", // 原始密码
                    new_password: "", // 新密码
                    confirm_password: "", // 确认密码
                },
                updateAvatarData: {
                    isVisible: false, // 弹窗
                    imageUrl: null,
                }
            }
        },
        methods: {
            // 成功上传头像
            handleAvatarSuccess(res, file) {
                this.updateAvatarData.imageUrl = URL.createObjectURL(file.raw);
                this.profileData.user_avatar = URL.createObjectURL(file.raw);
            },
            // 更新密码
            updatePassword: function () {
                let that = this;
                axios.post(that.profileData.updatePasswordUrl, {
                    original_password: that.updatePasswordData.original_password,
                    new_password: that.updatePasswordData.new_password,
                    confirm_password: that.updatePasswordData.confirm_password,
                })
                  .then(function (response) {
                      if (response.data.status !== 1) {
                          that.$message.error(response.data.info);
                      } else {
                          that.$message({message: response.data.info, type: "success"});
                          // 清理输入
                          that.updatePasswordData.confirm_password = "";
                          that.updatePasswordData.original_password = "";
                          that.updatePasswordData.new_password = "";
                          // 关闭弹窗
                          that.updatePasswordData.isVisible = false;
                      }
                  })
                  .catch(function (error) {
                      console.log(error);
                  });
            },
            // 更新职位
            updateJob: function () {
                let that = this;
                axios.post(that.profileData.updateJobUrl, {
                    job: that.form.job
                })
                  .then(function (response) {
                      if (response.data.status !== 1) {
                          that.$message.error(response.data.info);
                      } else {
                          that.$message({message: response.data.info, type: "success"});
                      }
                  })
                  .catch(function (error) {
                      console.log(error);
                  });
            },
            // 更新姓名
            updateName: function () {
                let that = this;
                axios.post(that.profileData.updateNameUrl, {
                    user_name: that.form.user_name
                })
                  .then(function (response) {
                      if (response.data.status !== 1) {
                          that.$message.error(response.data.info);
                      } else {
                          that.$message({message: response.data.info, type: "success"});
                      }
                  })
                  .catch(function (error) {
                      console.log(error);
                  });
            }
        },
    }
</script>

<style scoped>
    .clearfix:after {
        display: block;
        content: ' ';
        clear: both;
    }

    .container {
        width: 800px;
        margin: 0 auto;
    }

    .avatar-box {
        margin-top: 35px;
        text-align: center;
    }

    img.avatar {
        width: 100%;
    }
</style>

<style>
    .avatar-uploader .el-upload {
        border: 1px dashed #d9d9d9;
        border-radius: 6px;
        cursor: pointer;
        position: relative;
        overflow: hidden;
    }

    .avatar-uploader .el-upload:hover {
        border-color: #409EFF;
    }

    .avatar-uploader-icon {
        font-size: 28px;
        color: #8c939d;
        width: 178px;
        height: 178px;
        line-height: 178px;
        text-align: center;
    }
</style>