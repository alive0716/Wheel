# 安居客幸运大转盘活动开发

标签（空格分隔）： 项目设计

主要功能

 1. 页面整体布局
    
 2. 大转盘
    点击“开始抽奖“按钮，指针不动，转盘转动，转动3秒钟停止，抽奖行为完成；
    剩余抽奖次数：10次“，1个账号每天10次抽奖机会，完成1次抽奖（即抽奖结束弹出提示后）则抽奖机会减少1次，实时显示；
    点击“我的奖品”按钮，链接到“我的奖品“页面；
    点击“活动玩法”按钮，链接到“活动玩法“页面；
 3. 弹出窗口
    非注册登录用户点击“开始抽奖”按钮，则弹出弹窗,点击“马上注册/登录”按钮，跳转到如下APP注册登录页面(a.此处登录只允许安居客注册用户（允许手机号码验证登录，包含未设置密码用户），不包含第三方登录；b.点击“<”按钮，页面返回活动主页面；注册/登录过程中，点击“<”按钮则返回上一页；)
    第三方登录用户点击“开始抽奖”按钮，则弹出弹窗(a.点击“马上绑定”按钮，跳转到如下APP手机绑定页面：a.点击“<”按钮，页面返回活动主页面；b.注册/登录过程中，点击“<”按钮则返回上一页；)
    注册登录用户点击“开始抽奖”按钮，依据中奖情况页面展示相应的奖品活动也弹框
    10次抽奖机会用完，展示相应弹框
 4. 滚动字幕
    当获奖名单展示少于10条（即不满10位用户参加活动），模块文案部分不显示
    当获奖名单展示达到10条（即满10位及以上用户参加活动）模块文案部分显示，上下进行滚动
    获奖名单实时滚动展示，展示规则：名单自下而上滚动；刷新即更新滚动展示数据库；

技术方案
###页面整体布局
    13个页面布局
###大转盘
 - 获取后端奖项返回数据
 ```javascript
 function lottery() {
		$.ajax({
			type: 'post',
			url: getAwardUrl,
			dataType: 'json',
			error: function() {},
			success: function(data) {
				if (undefined != data && data.error == 0) {
					data = data.data;
					var prize = data.award_id;
					var times = data.play_num;
					var oAward = awardAngle(prize);
					if (times <= 10) {
						$('div.turntable-disk').rotate({
							duration: 3000,
							angle: 0,
							animateTo: 1440 + oAward,
							easing: $.easing.easeOutSine,
							callback: function() {
								var lastTimes = 10 - times;
								if (lastTimes < 0) {
									lastTimes = 0;
								}
								$('#play_num').html(lastTimes);

								//弹出对应的窗口
								if (prize == 0) {
									setTimeout(function() {
										dialog($('#overlay-restart'));
									}, 1000)
								} else {
									setTimeout(function() {
										dialog($('#overlay-award'));
									}, 1000)
								}
							}
						})

					} else {

						$('#play_num').html(0);
						dialog($('#overlay-over'));
					}
				} else {
					dialog($('#overlay-restart'));
				}
			}
		})

	}

 ```
 - 奖项奖项角度处理
  ```javascript
 function awardAngle(index) {
		var angle = 0,
			modalAward = $('#modal-award');
		switch (index) {
			case 0:
				angle = randomNum(317, 358); //要加油哦！
				break;
			case 1:
				angle = randomNum(272, 313); //5万元购房基金
				modalAward.attr('class', 'modal-award modal-award-1');
				break;
			case 2:
				angle = randomNum(227, 268); //100手机话费
				modalAward.attr('class', 'modal-award modal-award-2');
				//modalAward.addClass('modal-award-2');
				break;
			case 3:
				angle = randomNum(182, 223); //5元手机话费
				modalAward.attr('class', 'modal-award modal-award-3');
				//modalAward.addClass('modal-award-3');
				break;
			case 4:
				angle = randomNum(137, 178); //红米手机
				modalAward.attr('class', 'modal-award modal-award-4');
				//modalAward.addClass('modal-award-4');
				break;
			case 5:
				angle = randomNum(92, 133); //2元手机话费
				modalAward.attr('class', 'modal-award modal-award-5');
				//modalAward.addClass('modal-award-5');
				break;
			case 6:
				angle = randomNum(47, 88); //50元手机话费
				modalAward.attr('class', 'modal-award modal-award-6');
				//modalAward.addClass('modal-award-6');
				break;
			case 7:
				angle = randomNum(2, 43); //10元手机话费
				modalAward.attr('class', 'modal-award modal-award-7');
				//modalAward.addClass('modal-award-7');
				break;
		}
		return angle;
	}

 ```
 - 获取2个值之间的随机数
 ```javacsript
 function randomNum(nMin, nMax) {
		var range = nMax - nMin;
		var rand = Math.random();
		return (nMin + Math.round(range * rand))
	}
	
 ```
 - 弹框
  ```javacsript
 function dialog(obj) {
		obj.show();
		obj.find('.modal-close').bind('click', function() {
			obj.hide();
		})
	}
	
 ```
 - 手机号码验证
   ```javacsript
 function isPhone(aPhone) {
		var bValidate = RegExp(/^(0|86|17951)?(13[0-9]|15[012356789]|18[0-9]|14[57])[0-9]{8}$/).test(aPhone);
		if (bValidate) {
			return true;
		} else
			return false;
	}
	
 ```
 - 倒计时60秒
 ```javascript
 function time(obj, wait) {
		if (wait == 0) {
			obj.attr('disabled', false);
			obj.val('获取验证码');
			wait = wait;
		} else {
			obj.val('发送中(' + wait + ')');
			obj.attr('disabled', true);
			wait--;
			setTimeout(function() {
				time(obj, wait)
			}, 1000)
		}
	}

 ```
 - 安卓2.3系统webview不支持网页的div滚动条
 ```javascript
 function noBarsOnTouchScreen(arg) {
		var elem, tx, ty;
		if ('ontouchstart' in document.documentElement) {
			if (elem = document.getElementById(arg)) {
				elem.style.overflow = 'hidden';
				elem.ontouchstart = ts;
				elem.ontouchmove = tm;
			}
		}

		function ts(e) {
			var tch;
			if (e.touches.length == 1) {
				e.stopPropagation();
				tch = e.touches[0];
				tx = tch.pageX;
				ty = tch.pageY;
			}
		}

		function tm(e) {
			var tch;
			if (e.touches.length == 1) {
				e.preventDefault();
				e.stopPropagation();
				tch = e.touches[0];
				this.scrollTop += ty - tch.pageY;
				ty = tch.pageY;
			}
		}
	}
 
 ```
项目排期
1. 页面整体布局8.20~8.21
2. 大转盘8.22~8.25
3. 弹出窗口及滚动字幕8.25~8.26
4. 后端联调8.27~8.28