# Api 突然报 Network Error, 且请求无法到达服务端代码逻辑

答：后端说是因为某个子接口处理时间过长，导致整个接口触发亚马逊服务配置的异常逻辑，才报的 Network error

# 经确认是接口耗时过长导致（3-5s即可认定过长），给指定接口设置超时时间可临时解决此问题，如下：

![image](https://github.com/Jooezc/blog/assets/15683811/1f28e4b3-1570-49df-a586-96309c981b53)

# 改造Axios, 增加 timeout 设置:

![image](https://github.com/Jooezc/blog/assets/15683811/d4d47e05-321a-4830-b423-02347477c7ff)

![image](https://github.com/Jooezc/blog/assets/15683811/526e0182-3ad8-432f-aade-1619e1130e00)


