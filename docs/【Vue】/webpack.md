# Webpack

在build的webpack.base.conf.js文件上，可以配置一些路径别名（在resolve对象的alias对象下配），常见就是@代表src文件夹。通常下

```js
resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
        '@': resolve('src'),
        'assets': resolve('src/assets'),    		
        'components': resolve('src/components'),
        'views': resolve('src/views'),
    }    
}
```

注：普通的html标签下的src属性要用别名路径，注意前面加一个`~`

