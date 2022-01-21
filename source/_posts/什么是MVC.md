---
title: 什么是MVC
tags:
  - MVC
---

### 什么是 MVC

1. 最上面的一层，是直接面向最终用户的"视图层"（View）。它是提供给用户的操作界面，是程序的外壳。
2. 最底下的一层，是核心的"数据层"（Model），也就是程序需要操作的数据或信息。
3. 中间的一层，就是"控制层"（Controller），它负责根据用户从"视图层"输入的指令，选取"数据层"中的数据，然后对其进行相应的操作，产生最终结果。

<!-- more -->

Model 和服务器交互，Model 将得到的数据交给 Controller，Controller 把数据填入 View，并监听 View
用户操作 View，如点击按钮，Controller 就会接受到点击事件，Controller 这时会去调用 Model，Model 会与服务器交互，得到数据后返回给 Controller，Controller 得到数据就去更新 View

### MVC 例子

```javascript
!function () {
  let model = Model({
    resouceName: 'Message'
  })
  let view = View('.message')[0]
  let controller = {
    view: null,
    leaveMessage: null,
    messageList: null,
    model: null,
    init: function (view, model) {
      this.view = view
      this.model = model
      this.leaveMessage = view.querySelector('#leaveMessage')
      this.messageList = view.querySelector('ul')
      this.model.init()
      this.messageInit()
      this.bindEvents()
    },
    messageInit: function () {
      this.model.fetch().then(message => {
        let array = message.map(value => value.attributes)

        array.forEach((value, key) => {
          let oLi = document.createElement('li')
          let ospanname = document.createElement('span')
          let ospanmessage = document.createElement('span')
          ospanname.innerText = value.name + ': '
          ospanmessage.innerText = value.message

          oLi.appendChild(ospanname)
          oLi.appendChild(ospanmessage)

          this.messageList.appendChild(oLi)
        })
      })
    },
    bindEvents: function () {
      this.leaveMessage.addEventListener('submit', e => {
        e.preventDefault()
        this.savemessage()
      })
    },
    savemessage: function () {
      leaveMessage = this.leaveMessage
      let postName = leaveMessage.querySelector('input[name=name]').value
      let postMessages = leaveMessage.querySelector('input[name=messages]').value

      this.model
        .save({
          name: postName,
          message: postMessages
        })
        .then(function (object) {
          alert('提交成功')
          let ul = view.querySelector('ul')
          let oLi = document.createElement('li')
          let ospanname = document.createElement('span')
          let ospanmessage = document.createElement('span')
          ospanname.innerText = object.attributes.name + ': '
          ospanmessage.innerText = object.attributes.message

          oLi.appendChild(ospanname)
          oLi.appendChild(ospanmessage)

          ul.appendChild(oLi)
        })
    }
  }

  controller.init.call(controller, view, model)
}.call()
```
