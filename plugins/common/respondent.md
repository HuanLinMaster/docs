# 快捷回复 (Respondent)

@koishijs/plugin-respondent 允许设置一套内置问答，就像这样：

```ts title=koishi.ts
export default {
  plugins: {
    respondent: [{
      match: 'awsl',
      reply: '爱我苏联',
    }, {
      match: /^\s*(\S +){2,}\S\s*$/,
      reply: '空格警察，出动！',
    }, {
      match: /^(.+)一时爽$/,
      reply: (_, str) => `一直${str}一直爽`,
    }],
  },
}
```

<chat-panel>
<chat-message nickname="Alice">awsl</chat-message>
<chat-message nickname="Koishi">爱我苏联</chat-message>
<chat-message nickname="Bob">久 等 了</chat-message>
<chat-message nickname="Koishi">空格警察，出动！</chat-message>
<chat-message nickname="Carol">挖坑一时爽</chat-message>
<chat-message nickname="Koishi">一直挖坑一直爽</chat-message>
</chat-panel>

如果想要加入更高级和用户可定义的问答系统，可以参见 [koishi-plugin-dialogue](https://dialogue.koishi.chat)。

## 配置项

### options.rules

- 类型: `Array`

`respondent` 的规则数组，按照从上到下的顺序进行匹配。

### options.rules[].match

- 类型: `string | RegExp`

要匹配的内容。

### options.rules[].reply

- 类型: `string | ((...capture: string[]) => string)`

要做出的响应。

这个属性的行为类似于 [`String.prototype.replace()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace#%E5%8F%82%E6%95%B0)：

- 如果 `reply` 是字符串，则直接输出此字符串作为响应
- 如果 `reply` 是函数，则传入 match 的匹配结果作为参数，输出返回的字符串作为响应
