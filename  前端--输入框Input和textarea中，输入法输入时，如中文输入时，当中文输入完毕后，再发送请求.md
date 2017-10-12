####  前端--输入框Input和textarea中，输入法输入时，如中文输入时，当中文输入完毕后，再发送请求
#### 举例说明问题：
+ 传统的方式：如果输入‘有道’，键盘键入时，为youdao，当输入y时，onChange事件监听到该事件后，就会触发，发送请求；
+ 目的时：当中文输入时，当‘有道’字样输入完毕后，再发送请求；英文输入时，根据实时输入的内容，发送请求；
#### 参考链接为：https://segmentfault.com/a/1190000008023476
##### demo
```
/* eslint-disable import/no-extraneous-dependencies */
import React, { Component } from 'react';

class TestInput extends Component {
  constructor(props) {
    super(props);
    this.state = {
      inputValue: '',
      innerValue: '',
    };
    this.isOnComposition = false;
    this.isInnerChangeFromOnChange = false;
  }

  handleComposition = (e) => {
    // detect it is Chrome browser?
    const isChrome = !!window.chrome && !!window.chrome.webstore;
    const isFirefox = navigator.userAgent.indexOf('Firefox');
    const isOpera = navigator.userAgent.indexOf('Firefox') > -1;
    const isIE = navigator.userAgent.indexOf('compatible') > -1 && navigator.userAgent.indexOf('MSIE') > -1 && !isOpera;
    const isEdge = navigator.userAgent.indexOf('Edge') > -1;
    const isSafari = navigator.userAgent.indexOf('Safari') > -1;

    // console.log('type ', e.type, ', target ', e.target, ',target.value ', e.target.value, ', data', e.data)
    // Flow check
    if (!(e.target instanceof HTMLInputElement)) return;
    if (e.type === 'compositionend') {
     // Chrome is ok for only setState innerValue
     // Opera, IE and Edge is like Chrome
      if (isChrome || isIE || isEdge || isOpera) {
        this.setState({ innerValue: e.target.value });
      }
      // Firefox need to setState inputValue again...
      if (isFirefox) {
        this.setState({ innerValue: e.target.value, inputValue: e.target.value });
      }
      // Safari think e.target.value in composition event is keyboard char,
      //  but it will fire another change after compositionend
      if (isSafari) {
        // do change in the next change event
        this.isInnerChangeFromOnChange = true;
      }
      this.isOnComposition = false;
    } else {
      this.isOnComposition = true;
    }
  }

  handleChange = (e) => {
    // console.log('change type ', e.type, ', target ', e.target, ', target.value ', e.target.value)
     // Flow check
    if (!(e.target instanceof HTMLInputElement)) return;
    if (this.isInnerChangeFromOnChange) {
      this.setState({ inputValue: e.target.value, innerValue: e.target.value });
      this.isInnerChangeFromOnChange = false;
      return;
    }
   // when is on composition, change inputValue only
   // when not in composition change inputValue and innerValue both
    if (!this.isOnComposition) {
      this.setState({
        inputValue: e.target.value,
        innerValue: e.target.value,
      });
    } else {
      this.setState({ inputValue: e.target.value });
    }
  }

  render() {
    return (
      <div>
        <input
          onCompositionStart={this.handleComposition}
          onCompositionUpdate={this.handleComposition}
          onCompositionEnd={this.handleComposition}
          onChange={this.handleChange}
        />
      </div>
    );
  }
}

export default TestInput;

```

