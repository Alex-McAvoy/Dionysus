<template>
	<view class="page">
		<view class="container">
			<view class="header">
				<text class="main-title">Dionysus</text>
				<text class="sub-title">狄俄尼索斯的美酒</text>
			</view>

			<view class="form-card">
				<input
					v-model="user_name"
					class="name-input"
					type="text"
					placeholder="请输入姓名"
					placeholder-class="input-placeholder"
				/>

				<button
					class="generate-btn"
					:disabled="is_generating"
					@click="HandleGeneratePoster"
				>
					{{ is_generating ? "正在生成..." : "生成认怂图" }}
				</button>
			</view>

			<view v-if="poster_image" class="result-card">
				<text class="result-title">生成结果</text>
			
				<image
					class="poster-preview"
					:src="poster_image"
					mode="widthFix"
					@click="HandlePreviewPoster"
				></image>
			
				<view class="action-buttons">
					<button
						class="regenerate-btn"
						:disabled="is_generating"
						@click="HandleGeneratePoster"
					>
						{{ is_generating ? "正在生成..." : "换一张" }}
					</button>
				
					<button class="save-btn" @click="HandleSavePoster">
						保存图片
					</button>
				</view>
			</view>
		</view>

		<canvas
			canvas-id="posterCanvas"
			id="posterCanvas"
			class="poster-canvas"
		></canvas>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user_name: "",
				poster_image: "",
				poster_title: "",
				poster_subtitle: "",
				poster_tips: "",
				current_date: "",
				is_generating: false,
				slogan_list: [
					"酒局状态：认怂选手",
					"本场表现：未饮先输",
					"酒桌战绩：气势先倒",
					"还没开喝，已经想撤",
					"酒量未知，认怂很快",
					"举杯之前，先行服软",
					"本轮对决：先怂为敬"
				],
				tips_list: [
					"喝不过没关系，认怂要及时。",
					"面子不重要，别把自己喝倒。",
					"战略性认输，也是一种智慧。",
					"酒桌有风险，举杯需谨慎。",
					"怂得坦荡，也算一种风度。"
				]
			}
		},
		methods: {
			// 生成图片
			HandleGeneratePoster() {
				// 去除首尾空格
				const user_name = this.user_name.trim()

				// 判断是否输入姓名
				if (!user_name) {
					uni.showToast({
						title: "请先输入姓名",
						icon: "none"
					})
					return
				}
				
				// 防止重复点击生成
				if (this.is_generating) {
					return
				}
				this.is_generating = true

				// 主标题
				this.poster_title = user_name
				// 随机副标题
				this.poster_subtitle = this.GetRandomItem(this.slogan_list)
				// 随机底部提示语
				this.poster_tips = this.GetRandomItem(this.tips_list)
				// 当前日期
				this.current_date = this.GetCurrentDate()
				// 延迟
				setTimeout(() => {
					this.DrawPoster()
				}, 100)
			},

			// 随机生成内容
			GetRandomItem(data_list) {
				const random_index = Math.floor(Math.random() * data_list.length)
				return data_list[random_index]
			},

			// 获取当前日期
			GetCurrentDate() {
				// 当前时间
				const current_time = new Date()
			
				// 年月日
				const year = current_time.getFullYear()
				const month = String(current_time.getMonth() + 1).padStart(2, '0')
				const day = String(current_time.getDate()).padStart(2, '0')
			
				// 返回格式化日期
				return year + '-' + month + '-' + day
			},
			
			// 绘制
			DrawPoster() {
				// 上下文
				const canvas_context = uni.createCanvasContext("posterCanvas", this)
				
				// 宽度高度
				const poster_width = 600
				const poster_height = 900

				// 背景
				canvas_context.setFillStyle("#f7f3ea")
				canvas_context.fillRect(0, 0, poster_width, poster_height)

				// 标题
				canvas_context.setFillStyle("#6b4f2a")
				canvas_context.setFontSize(34)
				canvas_context.setTextAlign("center")
				canvas_context.fillText("Dionysus", poster_width / 2, 90)

				// 副标题
				canvas_context.setFillStyle("#8c6a3b")
				canvas_context.setFontSize(22)
				canvas_context.fillText("酒局认证结果", poster_width / 2, 135)

				// 背景
				canvas_context.setFillStyle("#fffaf2")
				canvas_context.fillRect(60, 180, 480, 460)

				// 边框
				canvas_context.setStrokeStyle("#d8c29d")
				canvas_context.setLineWidth(3)
				canvas_context.strokeRect(60, 180, 480, 460)

				// 姓名
				canvas_context.setFillStyle("#2f2a26")
				canvas_context.setFontSize(48)
				canvas_context.setTextAlign("center")
				canvas_context.fillText(this.poster_title, poster_width / 2, 290)

				// 分隔线
				canvas_context.setStrokeStyle("#d8c29d")
				canvas_context.setLineWidth(2)
				canvas_context.beginPath()
				canvas_context.moveTo(150, 330)
				canvas_context.lineTo(450, 330)
				canvas_context.stroke()

				// 认证结果
				canvas_context.setFillStyle("#7a5a32")
				canvas_context.setFontSize(24)
				canvas_context.fillText("认证结果", poster_width / 2, 390)

				// 随机文案
				canvas_context.setFillStyle("#b03a2e")
				canvas_context.setFontSize(36)
				this.DrawWrappedText(
					canvas_context,
					this.poster_subtitle,
					poster_width / 2,
					470,
					340,
					50
				)

				// 底部提示语
				canvas_context.setFillStyle("#5f5a55")
				canvas_context.setFontSize(24)
				canvas_context.setTextAlign('center')
				const tips_end_y = this.DrawWrappedText(
					canvas_context,
					this.poster_tips,
					poster_width / 2,
					580,
					360,
					40
				)
				
				// 日期
				canvas_context.setFillStyle('#8c857d')
				canvas_context.setFontSize(20)
				canvas_context.setTextAlign('right')
				canvas_context.fillText(this.current_date, 520, tips_end_y + 38)
				canvas_context.setTextAlign('center')

				// 底部说明
				canvas_context.setFillStyle("#8c857d")
				canvas_context.setFontSize(20)
				canvas_context.setTextAlign("center")
				const description = "“这是酒吗？这是流淌的冷火，是忘忧的良方，是城市里的绿洲，是痛苦生活的解药，是有着温度的江河，是无数个清冷的夜里，沸腾在心底最深处的孤独。”"
				this.DrawWrappedText(
					canvas_context,
					description,
					poster_width / 2,
					670,
					480,
					30
				)
				canvas_context.setTextAlign("right")
				canvas_context.fillText("——酒国烈士", 540, 780)
				canvas_context.setTextAlign("center")
				canvas_context.setFillStyle("#6b4f2a")
				canvas_context.setFontSize(26)
				canvas_context.fillText("—— by Alex-McAvoy ——", poster_width / 2, 850)

				// 绘制
				canvas_context.draw(false, () => {
					// 导出为临时图片
					uni.canvasToTempFilePath(
						{
							canvasId: "posterCanvas",
							width: poster_width,
							height: poster_height,
							destWidth: poster_width,
							destHeight: poster_height,
							success: (res) => {
								// 生成地址
								this.poster_image = res.tempFilePath
								// 恢复生成状态
								this.is_generating = false
							},
							fail: (err) => {
								console.log("导出图片失败：", err)
								uni.showToast({
									title: "生成图片失败",
									icon: "none"
								})
								this.is_generating = false
							}
						},
						this
					)
				})
			},

			// 自动换行生成文字
			DrawWrappedText(canvas_context, text, center_x, start_y, max_width, line_height) {
				// 拆分为字符数组
				const char_list = text.split("")

				// 初始化
				let current_line = ""
				let current_y = start_y
				for (let i = 0; i < char_list.length; i++) {
					// 拼接测试字符串
					const test_line = current_line + char_list[i]
					// 测量测试字符串宽度
					const metrics = canvas_context.measureText(test_line)
					
					// 判断是否超出最大宽度
					if (metrics.width > max_width && current_line !== "") {
						// 绘制当前行
						canvas_context.fillText(current_line, center_x, current_y)
						// 下一行
						current_line = char_list[i]
						current_y += line_height
					} else {
						// 拼接当前行
						current_line = test_line
					}
				}

				// 绘制最后一行
				if (current_line) {
					canvas_context.fillText(current_line, center_x, current_y)
					return current_y
				}
			},
		
			// 预览
			HandlePreviewPoster() {
				// 判断图片是否已经生成
				if (!this.poster_image) {
					return
				}
			
				// 打开图片预览
				uni.previewImage({
					current: this.poster_image,
					urls: [this.poster_image]
				})
			},
			
			// 保存
			HandleSavePoster() {
				// 判断图片是否已经生成
				if (!this.poster_image) {
					uni.showToast({
						title: "请先生成图片",
						icon: "none"
					})
					return
				}
			
				// #ifdef H5
				// 创建下载链接
				const download_link = document.createElement("a")
			
				// 图片地址
				download_link.href = this.poster_image
				// 文件名称
				const timestamp = Date.now()
				download_link.download = "Dionysus_" + this.user_name.trim() + "_" + timestamp + ".png"
			
				// 添加到页面
				document.body.appendChild(download_link)
				// 下载
				download_link.click()
				// 移除下载链接
				document.body.removeChild(download_link)
				// #endif
			
				// #ifndef H5
				// 保存到系统相册
				uni.saveImageToPhotosAlbum({
					filePath: this.poster_image,
					success: () => {
						uni.showToast({
							title: "图片保存成功",
							icon: "success"
						})
					},
					fail: (err) => {
						console.log("保存图片失败：", err)
			
						uni.showToast({
							title: "图片保存失败",
							icon: "none"
						})
					}
				})
				// #endif
			},
		}
	}
</script>

<style>
	.page {
		min-height: 100vh;
		background: #f8f8f8;
	}

	.container {
		padding: 40rpx 30rpx 60rpx;
	}

	.header {
		display: flex;
		flex-direction: column;
		align-items: center;
		margin-top: 30rpx;
		margin-bottom: 40rpx;
	}

	.main-title {
		font-size: 52rpx;
		font-weight: bold;
		color: #6b4f2a;
	}

	.sub-title {
		margin-top: 12rpx;
		font-size: 28rpx;
		color: #8f8f94;
	}

	.form-card {
		padding: 30rpx;
		background: #ffffff;
		border-radius: 24rpx;
		box-shadow: 0 8rpx 24rpx rgba(0, 0, 0, 0.05);
	}

	.name-input {
		height: 88rpx;
		padding: 0 24rpx;
		border: 2rpx solid #e5dccf;
		border-radius: 16rpx;
		background: #fffdf9;
		font-size: 30rpx;
		color: #333333;
	}

	.input-placeholder {
		color: #b7b7b7;
	}

	.generate-btn {
		margin-top: 24rpx;
		background: #6b4f2a;
		color: #ffffff;
		border-radius: 16rpx;
		font-size: 30rpx;
	}

	.result-card {
		margin-top: 40rpx;
		padding: 30rpx;
		background: #ffffff;
		border-radius: 24rpx;
		box-shadow: 0 8rpx 24rpx rgba(0, 0, 0, 0.05);
	}

	.result-title {
		display: block;
		margin-bottom: 20rpx;
		font-size: 30rpx;
		font-weight: bold;
		color: #333333;
	}

	.poster-preview {
		width: 100%;
		border-radius: 16rpx;
		overflow: hidden;
	}

	.poster-canvas {
		position: fixed;
		left: -2000px;
		top: -2000px;
		width: 600px;
		height: 900px;
	}
	
	.action-buttons {
		display: flex;
		gap: 20rpx;
		margin-top: 30rpx;
	}
	
	.regenerate-btn,
	.save-btn {
		flex: 1;
		margin: 0;
		border-radius: 16rpx;
		font-size: 28rpx;
		color: #ffffff;
	}
	
	.regenerate-btn {
		background: #6b4f2a;
	}
	
	.save-btn {
		background: #b03a2e;
	}
	
	.regenerate-btn:active,
	.save-btn:active {
		opacity: 0.85;
	}
	
	
</style>