<template>
	<div class="layout-padding">
		<el-row>
			<el-col :span="24" style="border:1px solid #E4E7ED;">
				<draggable v-model="state.fileList" item-key="id">
					<template #item="{ element }">
						<div class="drag-element" style="display: inline-block; width: 100px; height: 100px; margin: 10px; color: gray;">
							<el-image :src="element.url" :fit="'fill'" class="drag-img" style="width:100px;height:100px;"/>
							<div class="drag-btns">
									<el-row>
										<el-col :span="1"
											><el-button :text="true" @click="preview(element.url)" style="float: left"
												><el-icon><ZoomIn /></el-icon></el-button></el-col
										><el-col :span="20"></el-col>
										<el-col :span="1"
											><el-button :text="true" @click="remove(element)" style="float: right"
												><el-icon><Delete /></el-icon
											></el-button>
										</el-col>
									</el-row>
							</div>
						</div>
					</template>
					<template #footer>
						<div style="margin-top:30px;">
							<el-upload class="avatar-uploader" :action="state.uploadUrl" :show-file-list="false" :on-success="handleUploadSuccess" :before-upload="beforeUpload">
								<el-button type="primary">点击上传</el-button>
							</el-upload>
						</div>
					</template>
				</draggable>
			</el-col>
		</el-row>
		<el-row>
			<el-col :span="24">
				<span>上传的数据</span>
				<div>{{ state.fileList }}</div>
			</el-col>
		</el-row>
		<!--上传文件预览弹窗-->
		<el-dialog v-model="state.preview.visible">
			<el-image :src="state.preview.url" :fit="'fill'" />
		</el-dialog>
	</div>
</template>

<script setup name="dragsortupload">
import { defineAsyncComponent, reactive, onMounted, ref } from 'vue';
import { ElMessageBox, ElMessage } from 'element-plus';
import { ZoomIn, Plus, Delete } from '@element-plus/icons-vue';
import draggable from 'vuedraggable';

const state = reactive({
	uploadUrl:"",
	drag: true,
	preview: {
		visible:false,
		url: '',
	},
	fileList: [
		{
			id: 1,
			name: 'json',
			url: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
		},
		{ id: 2, name: 'jack', url: 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg' },
	],
});

const handleUploadSuccess = (response, uploadFile) => {
	console.log(response,uploadFile);
};

const beforeUpload = (rawFile) => {
	if (rawFile.type != 'image/jpeg') {
		ElMessage.error('Avatar picture must be JPG format!');
		return false;
	} else if (rawFile.size / 1024 / 1024 > 2) {
		ElMessage.error('Avatar picture size can not exceed 2MB!');
		return false;
	}
	// 以下为测试方法，实际使用过程中需要改成上传成功后再将返回结果放入fileList的操作
	state.fileList.push({id:rawFile.uid,name:rawFile.name,url:"https://fuss10.elemecdn.com/a/3f/3302e58f9a181d2509f3dc0fa68b0jpeg.jpeg"});
	return true;
};

// 上传文件预览
const preview = (url) => {
	state.preview.visible = true
	state.preview.url = url
};

// 上传文件删除
const remove = (item) => {
	let index;
	let fileListTemp = state.fileList;
	for(let i=0;i<fileListTemp.length;i++){
		if(fileListTemp[i].id === item.id){
			index = i
		}
	}
	if(index){
		state.fileList.splice(index,1)
	}
};

// 页面加载时
onMounted(() => {});
</script>

<style scoped lang="scss">
.avatar-uploader .avatar {
	width: 178px;
	height: 178px;
	display: block;
}
</style>

<style>
.avatar-uploader .el-upload {
	border: 1px dashed var(--el-border-color);
	border-radius: 6px;
	cursor: pointer;
	position: relative;
	overflow: hidden;
	transition: var(--el-transition-duration-fast);
}

.avatar-uploader .el-upload:hover {
	border-color: var(--el-color-primary);
}

.el-icon.avatar-uploader-icon {
	font-size: 28px;
	color: #8c939d;
	width: 178px;
	height: 178px;
	text-align: center;
}
</style>
