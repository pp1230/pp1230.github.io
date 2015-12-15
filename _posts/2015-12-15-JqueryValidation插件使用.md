---
layout: post
title: 使用Jquery-Validation插件进行前端验证
description: jQuery Validate 插件为表单提供了强大的验证功能，让客户端表单验证变得更简单，同时提供了大量的定制选项，满足应用程序各种需求。该插件捆绑了一套有用的验证方法，包括URL和电子邮件验证，同时提供了一个用来编写用户自定义方法的API。所有的捆绑方法默认使用英语作为错误信息，且已翻译成其他 37 种语言。该插件是由 Jorn Zaefferer 编写和维护的，他是 jQuery 团队的一名成员，是 jQuery UI 团队的主要开发人员，是 QUnit 的维护人员。该插件在 2006 年 jQuery 早期的时候就已经开始出现，并一直更新至今。目前版本是 1.13.1。
keywords: jquery,html
---

<script src="http://code.jquery.com/jquery-1.11.1.min.js"></script>
<script src="http://jqueryvalidation.org/files/dist/jquery.validate.min.js"></script>
<script src="http://jqueryvalidation.org/files/dist/additional-methods.min.js"></script>
<script type="text/javascript">
$.validator.setDefaults({
	submitHandler: function() { alert("submitted!"); }
});
//方法
$().ready(function() {
	// validate the comment form when it is submitted
	$("#commentForm").validate();

	// validate signup form on keyup and submit
	$("#signupForm").validate({
		rules: {
			"user.firstname": "required",
			lastname: "required",
			username: {
				required: true,
				minlength: 2
			},
			password: {
				required: true,
				minlength: 5
			},
			confirm_password: {
				required: true,
				minlength: 5,
				equalTo: "#password"
			},
			email: {
				required: true,
				email: true
			},
			topic: {
				required: "#newsletter:checked",
				minlength: 2
			},
			agree: "required"
		},
		messages: {
			firstname: "Please enter your firstname",
			lastname: "Please enter your lastname",
			username: {
				required: "Please enter a username",
				minlength: "Your username must consist of at least 2 characters"
			},
			password: {
				required: "Please provide a password",
				minlength: "Your password must be at least 5 characters long"
			},
			confirm_password: {
				required: "Please provide a password",
				minlength: "Your password must be at least 5 characters long",
				equalTo: "Please enter the same password as above"
			},
			email: "Please enter a valid email address",
			agree: "Please accept our policy"
		}
	});

$( "#myform1" ).validate({
  rules: {
    field: {
      required: true,
      rangelength: [11, 11]
    }
  }
});

$( "#myform2" ).validate({
  rules: {
    mobile_phone: {
      require_from_group: [11, ".phone-group"]
    },
    home_phone: {
      require_from_group: [1, ".phone-group"]
    },
    work_phone: {
      require_from_group: [1, ".phone-group"]
    }
  }
});

	// propose username by combining first- and lastname
	$("#username").focus(function() {
		var firstname = $("#firstname").val();
		var lastname = $("#lastname").val();
		if(firstname && lastname && !this.value) {
			this.value = firstname + "." + lastname;
		}
	});

	//code to hide topic selection, disable for demo
	var newsletter = $("#newsletter");
	// newsletter topics are optional, hide at first
	var inital = newsletter.is(":checked");
	var topics = $("#newsletter_topics")[inital ? "removeClass" : "addClass"]("gray");
	var topicInputs = topics.find("input").attr("disabled", !inital);
	// show when newsletter is checked
	newsletter.click(function() {
		topics[this.checked ? "removeClass" : "addClass"]("gray");
		topicInputs.attr("disabled", !this.checked);
	});
});
</script>

<style type="text/css">
#commentForm { width: 500px; }
#commentForm label { width: 250px; }
#commentForm label.error, #commentForm input.submit { margin-left: 253px; }
#signupForm { width: 670px; }
#signupForm label.error {
	margin-left: 10px;
	width: auto;
	display: inline;
}
#newsletter_topics label.error {
	display: none;
	margin-left: 103px;
}
</style>

##Introduction

> This jQuery plugin makes simple clientside form validation easy, whilst still offering plenty of customization options. It makes a good choice if you’re building something new from scratch, but also when you’re trying to integrate something into an existing application with lots of existing markup. The plugin comes bundled with a useful set of validation methods, including URL and email validation, while providing an API to write your own methods. All bundled methods come with default error messages in english and translations into 37 other languages.

##导入js和css

{%highlight html linenos%}
<link rel="stylesheet" type="text/css" media="screen" href="css/screen.css" />
<script src="../lib/jquery.js" type="text/javascript"></script>
<script src="../jquery.validate.js" type="text/javascript"></script>
<script src="../additional-methods.min.js" type="text/javascript"></script>
{%endhighlight%}

##方法

<table class="reference notranslate">
<tr>
	<th width="10%">序号</th>
	<th width="30%">规则</th>
    <th width="60%">描述</th>
</tr>
<tr>
	<td>1</td>
    <td>required:true</td>
	<td>必须输入的字段。</td>
</tr>
<tr>
	<td>2</td>
    <td>remote:"check.php"</td>
	<td>使用 ajax 方法调用 check.php 验证输入值。</td>
</tr>
<tr>
	<td>3</td>
    <td>email:true</td>
	<td>必须输入正确格式的电子邮件。</td>
</tr>
<tr>
	<td>4</td>
    <td>url:true</td>
	<td>必须输入正确格式的网址。</td>
</tr>
<tr>
	<td>5</td>
    <td>date:true</td>
	<td>必须输入正确格式的日期。日期校验 ie6 出错，慎用。</td>
</tr>
<tr>
	<td>6</td>
    <td>dateISO:true</td>
	<td>必须输入正确格式的日期（ISO），例如：2009-06-23，1998/01/22。只验证格式，不验证有效性。</td>
</tr>
<tr>
	<td>7</td>
    <td>number:true</td>
	<td>必须输入合法的数字（负数，小数）。</td>
</tr>
<tr>
	<td>8</td>
    <td>digits:true</td>
	<td>必须输入整数。</td>
</tr>
<tr>
	<td>9</td>
    <td>creditcard:</td>
	<td>必须输入合法的信用卡号。</td>
</tr>
<tr>
	<td>10</td>
    <td>equalTo:"#field"</td>
	<td>输入值必须和 #field 相同。</td>
</tr>
<tr>
	<td>11</td>
    <td>accept:</td>
	<td>输入拥有合法后缀名的字符串（上传文件的后缀）。</td>
</tr>
<tr>
	<td>12</td>
    <td>maxlength:5</td>
	<td>输入长度最多是 5 的字符串（汉字算一个字符）。</td>
</tr>
<tr>
	<td>13</td>
    <td>minlength:10</td>
	<td>输入长度最小是 10 的字符串（汉字算一个字符）。</td>
</tr>
<tr>
	<td>14</td>
    <td>rangelength:[5,10]</td>
	<td>输入长度必须介于 5 和 10 之间的字符串（汉字算一个字符）。</td>
</tr>
<tr>
	<td>15</td>
    <td>range:[5,10]</td>
	<td>输入值必须介于 5 和 10 之间。</td>
</tr>
<tr>
	<td>16</td>
    <td>max:5</td>
	<td>输入值不能大于 5。</td>
</tr>
<tr>
	<td>17</td>
    <td>min:10</td>
	<td>输入值不能小于 10。</td>
</tr>
</table>

##使用实例

###1.在页面中写js，每个步骤都验证。

###Description: Makes the element require a given value range.

* Example:

{%highlight html linenos%}
<h1 id="banner"><a href="http://bassistance.de/jquery-plugins/jquery-plugin-validation/">jQuery Validation Plugin</a> Demo</h1>
<div id="main">

<form id="myform1">
<label for="field">Required, length 11: </label>
<input type="text" class="left" id="field" name="field">
<br/>
<input type="submit" value="Validate!">
</form>
<script type="text/javascript">
$.validator.setDefaults({
	submitHandler: function() { alert("submitted!"); }
});
$( "#myform1" ).validate({
  rules: {
    field: {
      required: true,
      rangelength: [11, 11]
    }
  }
});
</script>
{%endhighlight%}

---------------------------------------------------------
<h1 id="banner"><a href="http://bassistance.de/jquery-plugins/jquery-plugin-validation/">jQuery Validation Plugin Demo 1</a></h1>
<form id="myform1">
<label for="field">Required, length 11: </label>
<input type="text" class="left" id="field" name="field">
<br/>
<input type="submit" value="Validate!">
</form>
----------------------------------------------------------

###Description: Ensures a given number of fields in a group are complete.

* Example:

{%highlight html linenos%}
<form id="myform2">
<label for="mobile_phone">Mobile phone: </label>
<input class="left phone-group" id="mobile_phone" name="mobile_phone">
<br/>
<label for="home_phone">Home phone: </label>
<input class="left phone-group" id="home_phone" name="home_phone">
<br/>
<label for="work_phone">Work phone: </label>
<input class="left phone-group" id="work_phone" name="work_phone">
<br/>
<input type="submit" value="Validate!">
</form>
<script type="text/javascript">
$( "#myform2" ).validate({
  rules: {
    mobile_phone: {
      require_from_group: [11, ".phone-group"]
    },
    home_phone: {
      require_from_group: [1, ".phone-group"]
    },
    work_phone: {
      require_from_group: [1, ".phone-group"]
    }
  }
});
</script>
{%endhighlight%}

------------------------------------------------------------------
<h1 id="banner">jQuery Validation Plugin Demo 2</h1>
<p>Default submitHandler is set to display an alert into of submitting the form</p>
<form id="myform2">
<label for="mobile_phone">Mobile phone: </label>
<input class="left phone-group" id="mobile_phone" name="mobile_phone">
<br/>
<label for="home_phone">Home phone: </label>
<input class="left phone-group" id="home_phone" name="home_phone">
<br/>
<label for="work_phone">Work phone: </label>
<input class="left phone-group" id="work_phone" name="work_phone">
<br/>
	<input type="submit" value="Validate!"/>
</form>

-------------------------------------------------------------------

###Description: Complex form1.

* Example:

{%highlight html linenos%}
<form class="cmxform" id="commentForm" method="get" action="">
	<fieldset>
		<legend>Please provide your name, email address (won't be published) and a comment</legend>
		<p>
			<label for="cname">Name (required, at least 2 characters)</label>
			<input id="cname" name="name" minlength="2" type="text" required />
		<p>
			<label for="cemail">E-Mail (required)</label>
			<input id="cemail" type="email" name="email" required />
		</p>
		<p>
			<label for="curl">URL (optional)</label>
			<input id="curl" type="url" name="url" />
		</p>
		<p>
			<label for="ccomment">Your comment (required)</label>
			<textarea id="ccomment" name="comment" required></textarea>
		</p>
		
	</fieldset>
</form>
<p>
			<input id="submit"   class="submit" type="submit" value="Submit"/>
		</p>
<script type="text/javascript">
$.validator.setDefaults({
	submitHandler: function() { alert("submitted!"); }
});
$("#commentForm").validate();
</script>
{%endhighlight%}

-----------------------------------------------------------------------------
<h1 id="banner">jQuery Validation Plugin Demo 3</h1>
<form class="cmxform" id="commentForm" method="get" action="">
	<fieldset>
		<legend>Please provide your name, email address (won't be published) and a comment</legend>
		<p>
			<label for="cname">Name (required, at least 2 characters)</label>
			<input id="cname" name="name" minlength="2" type="text" required />
		</p>	
		<p>
			<label for="cemail">E-Mail (required)</label>
			<input id="cemail" type="email" name="email" required />
		</p>
		<p>
			<label for="curl">URL (optional)</label>
			<input id="curl" type="url" name="url" />
		</p>
		<p>
			<label for="ccomment">Your comment (required)</label>
			<textarea id="ccomment" name="comment" required></textarea>
		</p>
		
	</fieldset>
</form>
<p>
			<input id="submit"   class="submit" type="submit" value="Submit"/>
		</p>

-----------------------------------------------------------------------------

###2.使用公共方法，调用公共js.

　　在主页面的js中加入验证方法，在子页面提交调用通用方法验证。所以在提交时才会出现验证，输入时不验证。

* js中使用通用方法

{%highlight html linenos%}
//解决表单提交回传刷新问题
//使用默认validate判断
<script>
function submitFormByDivAndUrlDefaultValidate(form, divid, urlret) {
	var datastr = $(form).serialize();
	var urlstr = $(form).attr("attr_href");
	var form1 = $(form);
	if (!urlstr) {
		return false;
	}
/*	jQuery.validator.setDefaults({
		  debug: true,
		  success: "valid"
		});*/
	$.validator.setDefaults({
		debug: true
	});
	form1.validate();
/*	alert(datastr + "##1" + urlstr + "##1" +"form valid:"+form1.valid());*/
	if(form1.valid()){
		jQuery
		.ajax({
			global : false,
			type : 'POST',
			contentType : 'application/x-www-form-urlencoded;charset=UTF-8',
			url : urlstr,
			// data:"keywords="+$('#keywords').val()+"&courseId_key="+$('#courseId_key').val(),
			data : datastr,
			error : function() {
				layer.msg('操作异常！', {
					offset : 20,
					shift : 6
				});
			},
			// 将list数据放入div中
			success : function() {
				layer.open({
					content : "操作成功！",
					scrollbar : false
				});
				$('#' + divid).load(urlret);
			}
		});
	}
	else{
		layer.msg('请正确填写表单！', {
			offset : 20,
			shift : 6
		});
	}
}
</script>
{%endhighlight%}

* 页面中使用通用标志判断（以userform为例）

{%highlight html linenos%}
<form action="" attr_href="${url }" method="POST"
				class="form-horizontal" id="userform">

				<c:if test="${securityuser.uid != null }">
					<%-- 	<form:hidden path="id" /> --%>
					<input type="hidden" name="user.uid" value="${securityuser.uid } " />
					<input type="hidden" name="user.id" value="${securityuser.id } " />
					<input type="hidden" name="user.createTime"
						value="${securityuser.createTime } " />
				</c:if>

				<div class="form-group">
					<label class="col-xs-3 control-label no-padding-right">用户名
						:</label> <input class="col-xs-3" name="user.username"
						value="${securityuser.username}" type="text"  minlength="2"  maxlength="60" required/>*  2-60个字符<br />
				</div>
				<div class="form-group">
					<label class="col-xs-3 control-label no-padding-right">登录密码:</label>
					<input class="col-xs-3" name="user.password"
						value="${securityuser.password}" type="password" minlength="6"  maxlength="60" required/>*  6-60个字符<br />
				</div>
				<div class="form-group">
					<label class="col-xs-3 control-label no-padding-right">真实姓名
						:</label> <input class="col-xs-3" name="user.realname"
						value="${securityuser.realname}" type="text" minlength="2"  maxlength="20" required/>*  2-20个字符<br />
				</div>
<div class="form-group">
		<label class="col-md-3 control-label no-padding-right">性别 :</label>
<div class="col-md-3 no-padding-left">
			 <select class="form-control" name="user.sex" >
                    <option value="男"<c:if test="${'男' eq securityuser.sex}">selected</c:if>>男</option>
					<option value="女"<c:if test="${'女' eq securityuser.sex}">selected</c:if>>女</option>
					</select>
		</div>	
		</div>
				<div class="form-group">
					<label class="col-xs-3 control-label no-padding-right">昵称:</label>
					<input class="col-xs-3" name="user.nickname"
						value="${securityuser.nickname}" type="text" /><br />
				</div>
				<div class="form-group">
					<label class="col-xs-3 control-label no-padding-right">邮箱:</label>
					<input class="col-xs-3" name="user.email" 
						value="${securityuser.email}" type="email" /><br />
				</div>
				<div class="form-group">
					<label class="col-xs-3 control-label no-padding-right">移动电话:</label>
					<input class="col-xs-3" name="user.mobile" 
						value="${securityuser.mobile}" type="text" minlength="11"  maxlength="11"  number="required" required/>*  11位电话号码
				</div>
				<div class="form-group">
					<label class="col-xs-3 control-label no-padding-right">地址:</label>
					<input class="col-xs-3" name="user.address" 
						value="${securityuser.address}" type="text" /><br />
				</div>
				<div class="form-group">
					<label class="col-xs-3 control-label no-padding-right">身份证号码:</label>
					<input class="col-xs-3" name="user.idcard" 
						value="${securityuser.idcard}" type="text" minlength="15"  maxlength="18"  number="required"/><br />
				</div>

				<label class="col-xs-3 control-label no-padding-right">选择角色：</label>
				<div class="col-xs-9" style="text-align:left">
					<c:forEach var="list1" items="${securityroles}" varStatus="vs1">
						<label><input name="roles[${vs1.index}].id" 
							type="checkbox" value="<c:out value="${list1.id}"/>" /> <c:out
								value="${list1.name}" /></label>
						<c:if test="${vs1.index%5==0&&vs1.index!=0}">
							<br />
						</c:if>
					</c:forEach>


					<c:forEach var="list2" items="${securityuserroles}" varStatus="vs2">
						<label><input name="roles[${vs2.index+list1.size}].id" 
							type="checkbox" checked="checked" 
							value="<c:out value="${list2.securityRole.id}"/>" /> <c:out
								value="${list2.securityRole.name}" /></label>
					</c:forEach>
				</div>

			</form>
{%endhighlight%}

* 验证结果：
![uservalidate](../../../static/images/uservalidate.png)

####参考资料: [Jquery-Validation官网文档](http://jqueryvalidation.org/category/methods/)