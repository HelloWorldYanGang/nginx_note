日志模块ngx_errlog_module对于支持可变参数平台提供的三个接口


```
#define ngx_log_error(level, log, ...)                                        \
    if ((log)->log_level >= level) ngx_log_error_core(level, log, __VA_ARGS__)
    
#define 、ngx_log_debug
(level, log, args...)                                    \
    if ((log)->log_level & level)                                             \
        ngx_log_error_core(NGX_LOG_DEBUG, log, args)
        
void ngx_log_error_core(ngx_uint_t level, ngx_log_t *log, ngx_err_t err,
    const char *fmt, ...);
```

参数:   
#### (1)level   
当前这条日志的级别   
![image](https://note.youdao.com/yws/public/resource/f216d6a562448bd14a9134fa1866482b/xmlnote/4C59AE9442164D62A932762A65D25817/285)

使用ngx_log_error记录日志时，传入level的级别小于等于log参数中级别（通常在nginx.conf中配置），就会输出日志内容，否则被忽略

ngx_log_debug中level代表日志类型
![image](https://note.youdao.com/yws/public/resource/f216d6a562448bd14a9134fa1866482b/xmlnote/C5ACE5C3E4D14949AC22B20D5AD5483E/300)

#### （2）log

处理请求时，http_request_t中有一个ngx_log_t成员，可以传给ngx_log_error、ngx_log_debug记录日志。读取配置时，ngx_conf_t也有log成员记录日志



```
typedef u_char *(*ngx_log_handler_pt) (ngx_log_t *log, u_char *buf, size_t len);

struct ngx_log_s {
    ngx_uint_t           log_level; //日志级别
    ngx_open_file_t     *file;  //日志文件

    ngx_atomic_uint_t    connection; //连接数，不为0时输出到日志中
    ngx_log_handler_pt   handler;//记录日志时的回调函数
    void                *data;//模块自定义。例如http模块data为请求上下文
    char                *action;//当前动作，配合handler使用
};

```

#### （3）err
错误码，一般是执行系统调用失败后的errno参数，err不为0时，nginx会输出这个错误码及字符串形式的错误消息。

#### （4）fmt
格式化参数，类似用printf。


nginx提供的不支持可变参数的调试日志接口
![image](https://note.youdao.com/yws/public/resource/f216d6a562448bd14a9134fa1866482b/xmlnote/6FD1D48575974E27997D6418183C4A4E/339)


