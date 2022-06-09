### css动效演示

#### 动态标题1

~~~ sass
   <div class="reveal">sword art online</div>

   body {
    display: flex;
    height: 100vh;
    justify-content: center;
    align-items: center;
    text-align: center;
    background: #222;
  }

  .reveal {
    position: relative;
    display: flex;
    color: #6ee1f5;
    font-size: 2em;
    font-family: Raleway, sans-serif;
    letter-spacing: 3px;
    text-transform: uppercase;
    white-space: pre;

    span {
      opacity: 0;
      transform: scale(0);
      animation: fadeIn 2.4s forwards;
    }

    &::before,
    &::after {
      position: absolute;
      content: "";
      top: 0;
      bottom: 0;
      width: 2px;
      height: 100%;
      background: white;
      opacity: 0;
      transform: scale(0);
    }

    &::before {
      left: 50%;
      animation: slideLeft 1.5s cubic-bezier(0.7, -0.6, 0.3, 1.5) forwards;
    }

    &::after {
      right: 50%;
      animation: slideRight 1.5s cubic-bezier(0.7, -0.6, 0.3, 1.5) forwards;
    }
  }

  @keyframes fadeIn {
    to {
      opacity: 1;
      transform: scale(1);
    }
  }

  @keyframes slideLeft {
    to {
      left: -6%;
      opacity: 1;
      transform: scale(0.9);
    }
  }

  @keyframes slideRight {
    to {
      right: -6%;
      opacity: 1;
      transform: scale(0.9);
    }
  }

  let duration = 0.8;
  let delay = 0.3;
  let revealText = document.querySelector(".reveal");
  let letters = revealText.textContent.split("");
  revealText.textContent = "";
  let middle = letters.filter(e => e !== " ").length / 2;
  letters.forEach((letter, i) => {
    let span = document.createElement("span");
    span.textContent = letter;
    span.style.animationDelay = `${delay + Math.abs(i - middle) * 0.1}s`;
    revealText.append(span);
  });

~~~

>> 步骤： 先拿到文本，切割成单个字母，用span包裹重新放入div中，根据中间索引，计算delay显示的时间，左右两边框也加上延迟显示和抖动的效果。



#### Wave Loading

~~~ css

body {
  display: flex;
  height: 100vh;
  justify-content: center;
  align-items: center;
  background: #222;
}

.loading {
  display: flex;
}


.loading  .dot {
  position: relative;
  width: 2em;
  height: 2em;
  margin: 0.8em;
  border-radius: 50%;
}

.loading  .dot::before {
  position: absolute;
  content: "";
  width: 100%;
  height: 100%;
  background: inherit;
  border-radius: inherit;
  animation: wave 2s ease-out infinite;
}

@keyframes wave {
  50%,
  75% {
    transform: scale(2.5);
  }

  80%,
  100% {
    opacity: 0;
  }
}

<div class="loading">
  <div class="dot" style="background-color: #7ef9ff; animation-delay: 0.2s"></div>
  <div class="dot" style="background-color: #89cff0; animation-delay: 0.4s"></div>
  <div class="dot" style="background-color: #4682b4; animation-delay: 0.6s"></div>
  <div class="dot" style="background-color: #0f52ba; animation-delay: 0.8s"></div>
  <div class="dot" style="background-color: #000080; animation-delay: 1s"></div>
</div>
~~~

>> 步骤：主要是点元素，都增加before属性，继承背景色和原角度，定时缩放和淡化，一次循环显示即可。