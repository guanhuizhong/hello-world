# hello-world
GitHub中第一个库，对我来说意义非凡

今天的练习


<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>

<!-- 新 Bootstrap 核心 CSS 文件 -->
<link rel="stylesheet"
	href="${pageContext.request.contextPath}/bootstrap/css/bootstrap.min.css">

<!-- 可选的Bootstrap主题文件（一般不用引入） -->
<link rel="stylesheet"
	href="${pageContext.request.contextPath}/bootstrap/css/bootstrap-theme.min.css">

<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
<script src="${pageContext.request.contextPath}/js/jquery-1.11.3.js"></script>

<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script
	src="${pageContext.request.contextPath}/bootstrap/js/bootstrap.min.js"></script>
<style type="text/css">
body {
	padding-top: 40px;
	padding-bottom: 40px;
	background-color: #eee;
}

.bg {
	max-width: 80%;
	padding: 15px;
	margin: 0 auto;
}
</style>
<script type="text/javascript">
	function delCustomer(Customer_id){
		location.href="${pageContext.request.contextPath }/customer/delCustomer?id="+Customer_id
	}
	
	//当前页
 	var  pageNum =1;
	//总页数
 	var totalPage = 0;
	//综条数
 	var tatalCount = 0;
	//每页条数
 	var currentCount =5;
	
	var cid
	function  findOrder(Customer_id){
	   cid=	Customer_id 
		$("#t1").html("")
		$.post("${pageContext.request.contextPath }/order/findOrder",{id:Customer_id , pageNum:pageNum ,currentCount:currentCount}, function(data){
			var html = "";
			var json = eval("("+data+")");

			 for(var i = 0; i<json.currentContent.length; i++){
				 html += "<tr><td>"+ json.currentContent[i].orderNum+"</td><td>"+ json.currentContent[i].receiceInfo+"</td><td>"+  json.currentContent[i].price+"</td><td>"+ json.currentContent[i].customer.cusName+"</td><td><a  href='javascript:void(0)' onclick='delOrder(\""+ json.currentContent[i].orderNum+"\")' >删除</a></td></tr>"
			 }
			
			$("#t1").html(html)
			
			//分页信息处理
			pageNum=json.pageNum;
			totalPage=json.totalPage;
			totalCount=json.totalCount;
			currentCount=json.currentCount;
			    
	    			var pageHtml="<ul class=\"pagination\">";
					//1.判断是否可以向上翻页
					if(pageNum==1){
						pageHtml+="<li class=\"disabled\"><a href=\"#\">&laquo;</a></li>";
					}else{
						pageHtml+="<li><a href=\"#\" onclick=\"prePage()\">&laquo;</a></li>";
					}
					
					//2.判断中间页码
					for(var i=1;i<=totalPage;i++){
						if(i==pageNum){
							pageHtml+="<li class=\"active\"><a href=\"#\" onclick=\"selectPage("+i+")\">"+i+"</a></li>";
						}else{
							pageHtml+="<li><a href=\"#\" onclick=\"selectPage("+i+")\">"+i+"</a></li>";
						}
					}
					
					//3.判断是否可以向下翻页
					if(pageNum==totalPage){
						pageHtml+="<li class=\"disabled\"><a href=\"#\">&raquo;</a></li>";
					}else{
						pageHtml+="<li><a href=\"#\" onclick=\"nextPage()\">&raquo;</a></li>";
					}
					pageHtml+="</ul>";
					
					$("#page").html(pageHtml);
		});
	}
	
	//向上翻页
	function prePage(){
		pageNum=pageNum-1;
		findOrder(cid);
	}
	//向下翻页
	function nextPage(){
		pageNum=pageNum+1;
		findOrder(cid);
	}
	
	//指定页跳转
	function selectPage(pn){
		pageNum=pn;
		findOrder(cid);
	}
	    
	//删除订单
	function delOrder(orderNum){
		location.href="${pageContext.request.contextPath }/order/delOrder?orderNum="+orderNum
		//findOrder(cid);
	}
</script>

</head>
<body>

	<s:debug></s:debug>
	<table class="table table-bordered  bg">
		<tr>
			<td colspan="5"><a
				href="${pageContext.request.contextPath}/addCustomer.jsp"
				class="btn btn-primary btn-lg active" role="button">Add Customer</a></td>
		</tr>
		<tr>
			<td>序号</td>
			<td>用户</td>
			<td>名字</td>
			<td>电话</td>
			<td>操作</td>
		</tr>

		<s:iterator value="cs" var="c" status="vs">
			<tr>
				<td><s:property value="#vs.index+1" /></td>
				<td><img src="<s:property value='#c.cusImgSrc'/>"></td>
				<td><s:property value="#c.cusName" /></td>
				<td><s:property value="#c.cusphone" /></td>
				<td><a href="javascript:void(0)" class="btn btn-primary btn-sm"
					onclick="delCustomer(<s:property value="#c.id"/>)">删除客户</a> <a
					href="javascript:void(0)"
					onclick="findOrder(<s:property value="#c.id"/>)"
					class="btn btn-primary btn-sm" data-toggle="modal"
					data-target="#myModal">订单详情</a></td>
			</tr>
		</s:iterator>
	</table>

	<!-- Modal -->
	<div class="modal fade" id="myModal" tabindex="-1" role="dialog"
		aria-labelledby="myModalLabel">
		<div class="modal-dialog" role="document">
			<div class="modal-content">
				<div class="modal-header">
					<button type="button" class="close" data-dismiss="modal"
						aria-label="Close">
						<span aria-hidden="true">&times;</span>
					</button>
					<h4 class="modal-title" id="myModalLabel">Modal title</h4>
				</div>
				<div class="modal-body">
					<table class="table table-bordered .table-hover">
						<tr>
							<td>订单编号</td>
							<td>收货地址</td>
							<td>订单价格</td>
							<td>客户名称</td>
							<td>操作</td>
						</tr>
						<tbody id="t1">
						</tbody>
					</table>
				</div>
				<div class="modal-footer">
					<nav aria-label="Page navigation">
					<div class="modal-footer">
						<nav id="page"> </nav>
					</div>
					</nav>
				</div>
			</div>
		</div>
	</div>
</body>
</html>
