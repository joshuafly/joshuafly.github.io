﻿<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">
		<meta name="referrer" content="no-referrer" />
		<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/buijs@latest/lib/latest/bui.css">
		<script src="https://cdn.staticfile.org/jquery/2.2.4/jquery.min.js"></script>
		<script src="https://cdn.jsdelivr.net/npm/buijs@latest/lib/latest/bui.js"></script>
		<script src="https://cdn.jsdelivr.net/npm/clipboard@2.0.6/dist/clipboard.min.js"></script>
		<style>
			.panel-list {
				padding: 0 .1rem;
				border-top: 0;
				background: none;
			}
			.time {
				margin: .3rem 0;
				padding: 0 .2rem;
				height: .4rem;
				line-height: .4rem;
				border-radius: .4rem;
				background: #b7b6b5;
				color: #fff;
				font-size: .25rem;
			}
			.bui-list .bui-btn {
				border: 0;
				background: #f9f7f7;
				margin-bottom: .5rem;
			}
			.photo-title {
				line-height: .5rem;
				letter-spacing: .05rem;
			}
			.photo-title img {
				display: block;
				max-width: 5rem;
				border-radius: .15rem;
			}
			.photo-title.activity img {
				max-width: 100%;
			}
			.bui-tab .bui-nav .active {
				color: #FF7F50;
			}

			.bui-nav>[class*="bui-btn"].active::after {
				background: #FF7F50;
			}

			header.bui-bar,
			header .bui-bar {
				background: #FFFFFF;
			}
			#btnSearchDialog {
				background: #f3f5f8;
			}
			.tkl{
				color: #FF7F50;
				padding: 0 .05rem;
				font-weight: bolder;
			}
			a:link, a:visited { 
				color: #FF7F50;
			}
			#dialogCenter-mask {
				z-index: 101 !important;
			}
			#dialogCenter {
				z-index: 102 !important;
			}
			.itb {
				color: #52c41a;
			}
			.ijd {
				color: #39a4ff;
			}
			.ipdd {
				color: #6568ec;
			}
		</style>
	</head>
	<body>
		<div class="bui-page">
			<header class="bui-bar">
			</header>
			<main>
				<div id="uiTab" class="bui-tab">
					<div class="bui-tab-head">
						<ul class="bui-nav">
							<li class="bui-btn">实时线报</li>
							<li class="bui-btn">活动推荐</li>
						</ul>
					</div>
					<div class="bui-tab-main">
						<ul>
							<li class="xb">
								<header class="bui-bar">
									<div class="bui-bar-left" style="width: .2rem;"></div>
									<div class="bui-bar-main">
										<div id="btnSearchDialog" class="bui-input">
											<i class="icon-search"></i><span class="placeholder">搜索</span>
										</div>
									</div>
									<div class="bui-bar-right" style="width: .2rem;"></div>
								</header>
								<div class="panel-list">
									<div class="container">
										<div id="scrollList" class="bui-scroll">
											<div class="bui-scroll-head"></div>
											<div class="bui-scroll-main">
												<ul class="bui-list xblist">

												</ul>
											</div>
											<div class="bui-scroll-foot"></div>
										</div>
									</div>
								</div>
							</li>
							<li>
								<div class="panel-list">
									<div class="container">
										<ul class="bui-list hdlist">

										</ul>
									</div>
								</div>
							</li>
						</ul>
					</div>
				</div>

			</main>
		</div>
		<!-- 中间自定义弹出框结构	 -->
		<div id="dialogCenter" class="bui-dialog" style="display: none;">
			<div class="bui-dialog-main">
				<div class="restkl" style="margin: .5rem;"></div>
				<div class="bui-btn warning copybtn"></div>
			</div>
		</div>
		<div id="uiDialog" class="bui-dialog" style="display: none;">
			<div class="bui-bar">
				<div class="bui-bar-left">
					<a class="bui-btn" id="btnBack"><i class="icon-back"></i></a>
				</div>
				<div class="bui-bar-main">
					<div id="searchbar" class="bui-input ring mini">
						<i class="icon-search"></i>
						<input type="search" value="" placeholder="请输入关键字" />
					</div>
				</div>
				<div class="bui-bar-right">
					<div id="btnSearch" class="bui-btn">
						搜索</div>
				</div>
			</div>
			<div class="bui-dialog-main">

				<!-- 列表控件结构 -->
				<div id="scrollSearch" class="bui-scroll">
					<div class="bui-scroll-head"></div>
					<div class="bui-scroll-main">
						<ul class="bui-list searchlist">
						</ul>
					</div>
					<div class="bui-scroll-foot"></div>
				</div>
			</div>
		</div>
		<script>
			var httpServer = "http://8.129.123.115:80"
			//修改为线报服务端IP

			function getLocalTime(nS) {
				var date = new Date(nS);
				var YY = date.getFullYear() + '-';
				var MM = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-';
				var DD = (date.getDate() < 10 ? '0' + (date.getDate()) : date.getDate());
				var hh = (date.getHours() < 10 ? '0' + date.getHours() : date.getHours()) + ':';
				var mm = (date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes()) + ':';
				var ss = (date.getSeconds() < 10 ? '0' + date.getSeconds() : date.getSeconds());
				return YY + MM + DD +" "+hh + mm + ss;
			}
			bui.ready(function() {
				var ntm, ltm, ydi, ltm2
				var uiParams = bui.getPageParams();
				uiParams.done(function(result) {
					yid = result.id
				})
				var uiLoading = bui.loading({
					width: 40,
					height: 40,
					callback: function(argument) {}
				});
				var uiTab = bui.tab({
					id: "#uiTab"
				});

				var uiList = bui.list({
					id: "#scrollList",
					url: httpServer + "/getNewList",
					contentType: "",
					refresh: false,
					pageSize: 10,
					data: {
						ntm: ntm,
						ltm: ltm
					},
					field: {
						page: "page",
						size: "pageSize",
						data: "data"
					},
					callback: function(e) {
					},
					template: function(data) {
						var html = "";
						data.forEach(function(el, index) {
							if (el.tm) {
								ltm = el.tm;
								uiList.modify({
									data: {
										ltm: ltm
									}
								})
							}
							if (/^\d{13}$/.test(el.msg)) {
								$(".xblist li").each(function() {
									if ($(this).attr("did") == el.msg) {
										$(this).remove();
									}
								})
								return html;
							}
							var tmstr = getLocalTime(parseInt(el.tm))
							html +=
								`<li class="bui-btn bui-box" did="${el.tm}">
									<div class="span1">
										<div class="bui-box-center">
											<div class="time">${tmstr}</div>
										</div>
										<h3 class="photo-title">${el.msg}</h3>
									</div>
								</li>`
						});
						return html;
					},

				})
				var getActivityList = function() {
					$.ajax({
						url: httpServer + "/getActivityList",
						timeout: 5000
					}).then(function(res) {
						if (!res.data) return;
						$.each(res.data, function(idx, obj) {
							var html =
								`<li class="bui-btn bui-box" did="{tm}">
										<div class="span1">
											<h3 class="photo-title activity">{msg}</h3>
										</div>
									</li>`
							html = html.replace("{tm}", obj.tm)
							html = html.replace("{msg}", obj.msg)
							$(".hdlist").prepend(html)
						});
					}, function(res, status) {
					})
				}
				getActivityList()
				var prepending
				var getNewList = function() {
					if (prepending) return;
					prepending = true
					ntm = $('.xblist').find('li:first').attr("did")
					$.ajax({
						url: httpServer + "/getNewList",
						data: {
							ntm: ntm
						},
						timeout: 5000
					}).then(function(res) {
						if (!res.data) return;
						$.each(res.data, function(idx, obj) {
							if (/^\d{13}$/.test(obj.msg)) {
								$(".xblist li").each(function() {
									if ($(this).attr("did") == obj.msg) {
										$(this).remove();
									}
								})
								return;
							}
							var tmstr = getLocalTime(parseInt(obj.tm))
							var html =
								`<li class="bui-btn bui-box" did="{tm}">
										<div class="span1">
											<div class="bui-box-center">
												<div class="time">{tmstr}</div>
											</div>
											<h3 class="photo-title">{msg}</h3>
										</div>
									</li>`
							html = html.replace("{tm}", obj.tm)
							html = html.replace("{tmstr}", tmstr)
							html = html.replace("{msg}", obj.msg)
							$(".xblist").prepend(html)
						});
						prepending = false
					}, function(res, status) {
						prepending = false
					})
				}
				setInterval(getNewList, 30000)
				var uiDialogtkl = bui.dialog({
					id: "#dialogCenter",
					width: 300
				});
				var hitstr
				var clipboard = new ClipboardJS('.copybtn', {
					text: function(target) {
						return restkl;
					}
				});
				clipboard.on('success', function(e) {
					uiDialogtkl.close();
					e.clearSelection();
					bui.hint({
						content: hitstr,
						width: "80%",
						position: "center",
						effect: "fadeInDown"
					});
				});
				clipboard.on('error', function(e) {
					uiDialogtkl.close();
					e.clearSelection();
					bui.hint("内容复制失败，请手动复制")
				});
				$(".xblist,#scrollSearch,.hdlist").on("click", ".tkl", function() {
					if($(this).attr("did").indexOf("http") == -1){
						restkl = "1"+$(this).attr("did")+"/"
						$(".copybtn").text("复制淘口令")
						hitstr = "淘口令已复制，打开手机淘宝即可"
					}else{
						restkl = $(this).attr("did")
						$(".copybtn").text("复制网址")
						hitstr = "网址已复制"
					}
					$(".restkl").text(restkl)
					uiDialogtkl.open();
				})

				var uiListsearch;
				var uiDialog = bui.dialog({
					id: "#uiDialog",
					fullscreen: true,
					mask: false,
					effect: "fadeInRight"
				});
				// var n = 0;
				//搜索条的初始化
				var uiSearchbar = bui.searchbar({
					id: "#searchbar",
					onInput: function(ui, keyword) {},
					onRemove: function(ui, keyword) {},
					callback: function(ui, keyword) {

						if (uiListsearch) {
							ltm2 = null
							uiListsearch.empty();
							uiListsearch.init({
								page: 1,
								data: {
									"keyword": keyword,
									ltm: ltm2
								},
								onRefresh: onRefresh,
								onLoad: onLoad
							});

						} else {
							// 列表初始化
							uiListsearch = bui.list({
								id: "#scrollSearch",
								url: httpServer + "/search",
								contentType: "",
								field: {
									data: "data"
								},
								data: {
									"keyword": keyword
								},
								page: 1,
								pageSize: 10,
								onRefresh: onRefresh,
								onLoad: onLoad,
								template: function(data) {
									var html = "";
									data.forEach(function(el, index) {
										if (el.tm) {
											ltm2 = el.tm;
											uiListsearch.modify({
												data: {
													ltm: ltm2
												}
											})
										}
										if (/^\d{13}$/.test(el.msg)) {
											$(".xblist li").each(function() {
												if ($(this).attr("did") == el.msg) {
													$(this).remove();
												}
											})
											return html;
										}
										var tmstr = getLocalTime(parseInt(el.tm))
										html +=
											`<li class="bui-btn bui-box" did="${el.tm}">
												<div class="span1">
													<div class="bui-box-center">
														<div class="time">${tmstr}</div>
													</div>
													<h3 class="photo-title">${el.msg}</h3>
												</div>
											</li>`
									});
									return html;
								},
								callback: function(argument) {
									console.log($(this).text())
								}
							});


						}

						// 下拉刷新
						function onRefresh() {

						}
						// 上拉加载
						function onLoad() {

						}

					}

				});

				$("#btnSearchDialog").on("click", function(argument) {

					uiDialog.open();
				})
				$("#btnBack").on("click", function(argument) {
					uiDialog.close();
				})
				$("#btnSearch").on("click", function(argument) {
					uiSearchbar.search();
				})

			})
		</script>
	</body>
</html>
