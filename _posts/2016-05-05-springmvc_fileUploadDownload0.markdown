---
layout: post
title: 基于springmvc框架实现上传下载业务（二）
date: 2016-05-05 18:53:22
categories: blog
author:     "jarvis"
tags:
    - java上传
    - java下载
---

> 文件下载功能

action层

``` java
@RequestMapping("/fileDownload")
    @ResponseBody
    public void fileDownload(HttpServletRequest request, HttpServletResponse response) throws IOException, ServletException {
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        OutputStream out = null;
        String downPath = "E:/test/upload/a.text";//文件路径名固定了，实际可发可能是根据id查询或者传值方式确定文件名
        File file = new File(downPath);

        if (file.exists()) {
            /**
             * 设置要下载文件的文件类型 application/octet-stream：二进制传输数据
             */
            response.setContentType("application/octet-stream");
            /**
             * response.setHeader设置头部，Content-disposition告诉浏览器下载的文件名
             */
            response.setHeader("Content-disposition", "attachment;filename=" + URLEncoder.encode(file.getName(), "UTF-8"));
            bis = new BufferedInputStream(new FileInputStream(file.getPath()));
            out = response.getOutputStream();
            bos = new BufferedOutputStream(out);
            int len = -1;
            byte[] b = new byte[8192];
            while ((len = bis.read(b)) != -1) {
                bos.write(b, 0, len);
            }
            if (bis != null) bis.close();
            if (bos != null) bos.close();

        } else {
            request.getRequestDispatcher("/info.jsp").forward(request, response);
        }
    }
```

``` html
  <a href="${pageContext.request.contextPath }/fileAction/fileDownload">点击下载</a>
```

###### 新建了一个info提示页面，名为info.jsp,页面内容为：

``` html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<body>
资源文件不存在
</body>

```

*该下载仅做了功能上的实现，逻辑漏洞并未完善*

>**PS:**说一下在做开发过程中会遇到的问题和关键点

* 下载要设置两个关键信息setContentType和SetHeader

* 下载常用方式 通过a标签的href属性指向action层或是通过js事件调用window.open(URL)的方式

* 下载如果是针对文件夹，可使用zip流先进行文件夹的打包处理
