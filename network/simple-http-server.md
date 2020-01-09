# 简单的HTTP Server

实现简单的HTTP Server

## 确定使用场景与约束

1. 处理请求方法
    * GET
    * POST
2. 识别请求头
    * Connection
    * Accept-Type
    * ContentType
3. 实现功能
    * 处理Multipart类型请求
        * 表单提交
        * 文件上传
4. 使用语言
    * Java
    * 只能使用标准库
    * 不使用第三方库与框架（Spring、Netty等）
5. 测试工具
    * Postman
6. 进阶
    * 探究Netty的原理
    * Netty的解码/编码
    * TCP沾包/拆包问题
    * 自定义协议的解析
7. 可以考虑添加的额外功能
    * 支持SSL/TLS

## 确定架构

## 编写核心组件实现