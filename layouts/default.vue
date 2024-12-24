<script setup>
function sizeMonitorProperly() {
  const m = document.querySelector('#computerMonitor')
  const cs = getComputedStyle(m)
  const distance = Number.parseFloat(getComputedStyle(document.body).getPropertyValue('--distance'))
  const width = Number.parseFloat(`0.${cs.getPropertyValue('--width').trim()}`) * document.body.getBoundingClientRect().width // in px
  const angle = Number.parseFloat(cs.getPropertyValue('--angle'))
  const theta = Math.atan(distance / width) * 180 / Math.PI
  const theta2 = 180 - theta
  const theta3 = 180 - theta2 - angle
  const theta4 = 90 - theta3
  const temp = width * Math.sin(angle * Math.PI / 180)
  const g1 = width * Math.cos(angle * Math.PI / 180)
  const g2 = temp * Math.tan(theta4 * Math.PI / 180)
  const goal = g1 + g2
  const scaleFactor = goal / width
  m.style.setProperty('--scale', scaleFactor)
}

function removeFromArray(arr, index) {
  return [...arr.slice(0, index), ...arr.slice(index + 1)]
}

onMounted(() => {
  sizeMonitorProperly()
  window.addEventListener('resize', sizeMonitorProperly)
  window.addEventListener('load', () => {
    const minimum = Math.min(fakeUsers.length, fakeComments.length, fakeIcons.length)
    for (let i = 0; i < minimum; i++) {
      const usernameindex = Math.floor(Math.random() * fakeUsers.length)
      const username = fakeUsers[usernameindex]
      fakeUsers = removeFromArray(fakeUsers, usernameindex)
      const commentindex = Math.floor(Math.random() * fakeComments.length)
      const comment = fakeComments[commentindex]
      fakeComments = removeFromArray(fakeComments, commentindex)
      const iconindex = Math.floor(Math.random() * fakeIcons.length)
      const icon = fakeIcons[iconindex]
      fakeIcons = removeFromArray(fakeIcons, iconindex)
      const useStatus = !Math.floor(Math.random() * 3) // one in 3 chance of being true
      let status = null
      if (useStatus && fakeStatuses.length > 0) {
        const statusindex = Math.floor(Math.random() * fakeStatuses.length)
        status = fakeStatuses[statusindex]
        fakeStatuses = removeFromArray(fakeStatuses, statusindex)
      }
      createPost({ username, status }, icon, comment)
    }
  })
})
</script>

<template>
  <div class="viewportBlur blur2" />
  <div class="viewportBlur" />
  <div id="computerMonitor">
    <div id="window">
      <div class="pixels" />
      <nav>
        ATLAS.
      </nav>
      <main>
        <slot />
      </main>
    </div>
  </div>
</template>

<style lang="scss" scoped>
#test {
  position: absolute;
  top: 0;
  left: 0;
  background-color: #00f4;
  color: white;
  z-index: 999;
}

html, body {
  width: 100%;
  height: 100%;
  margin: 0;
  font-family: arial;
  // background-color: black;
}

body {
  overflow: hidden;
  font-size: 14px;
  --distance: 1000px;
  perspective: var(--distance);
  perspective-origin: 50% 50%;
  display: flex;
  align-items: center;
  background-color: black;

  &:before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    // old image: https://cdn.pixabay.com/photo/2017/08/06/06/18/rain-2589417_960_720.jpg
    background: url('https://cdn.pixabay.com/photo/2017/08/06/06/18/rain-2589417_960_720.jpg') center;
    background-size: cover;
    // transform: scaleX(-1);
  }

  h1, h2, h3, h4, h5, h6 {
    margin-top: .2em;
    margin-bottom: .2em;
  }
  h1 {
    font-size: 24px;
  }
  h2 {
    font-size: 20px;
  }
  h3 {
    font-size: 10px;
  }
  hr {
    width: 100%;
    margin: 1.2em 0;
  }
}

.blurLayer {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 10;
  background-color: transparent;
  filter: blur(3px);
  mask: linear-gradient(to right, transparent, black);
}

.viewportBlur {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  -webkit-mask-image: linear-gradient(to right, transparent, red);
  mask-image: linear-gradient(to right, transparent, red);
  backdrop-filter: blur(3px);
  z-index: 10;
  pointer-events: none;

  &.blur2 {
    -webkit-mask-image: linear-gradient(to right, transparent, transparent, red);
    mask-image: linear-gradient(to right, transparent, transparent, red);
  }
}

#computerMonitor {
  --width: 85vw;
  --borderSize: 20px;
  border: black var(--borderSize) solid;
  border-radius: 8px;
  position: relative;
  box-sizing: border-box;
  background-color: #F8F8F8;
  width: var(--width);
  // position: relative;
  left: 3vw;
  // height: 50vh;
  aspect-ratio: 16 / 9;
  // --angle: 19deg;
  // --angle: 8deg;
  --angle: 12.5deg;
  // --angle: 24deg;

  // --angle: 0deg;
  // --scale: 1.7;
  // --scale: 2.3;
  // --scale: 1.8;
  --scale: calc(1 / cos(var(--angle)));
  overflow: auto;
  transform: rotateY(var(--angle)) scaleX(var(--scale)) scaleY(var(--scale)); //translateZ(500px);
  transform-origin: center left;

}

#window {
  background-color: #F8F8F8;
  position: relative;
}

.pixels {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  // background-color: #f005;
  background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAMAAAABCAYAAAAb4BS0AAAAEklEQVQIW2P8z8DwnxFIMAAJAB4HBABhGG2qAAAAAElFTkSuQmCC');
  background-size: 3px;
  opacity: .2;
  z-index: 20;
  pointer-events: none;
}

nav, main {
  padding: .8em 2vw;
}

nav {
  background-color: #080808;
  color: white;
  font-weight: bold;
  font-size: 30px;
}

main {
  display: flex;
  flex-direction: column;
}

#post, #comments, #sidebar {
  --paddingRL: 3.2em;
  padding: .2em var(--paddingRL);
}

#post {

  text-align: justify;
  padding-top: .8em;
  padding-bottom: .8em;
  // margin-bottom: 1.6em;

  .imagecontainer {
    --spaceFromText: 1em;
    float: right;
    width: 52%;
    height: 90%;
    position: relative;
    margin: 0 0 var(--spaceFromText) calc(var(--spaceFromText) - var(--paddingRL));
    left: var(--paddingRL);
    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
  }
}

.ratingsPanel {
  display: flex;
  justify-content: space-between;
  align-items: center;
  &>div:last-child {
    display: flex;
    &>button {
      width: 3ch;
      border-radius: 0;
      border: none;
      background-color: transparent;
      color: #888f;
      font-weight: bold;
      filter: grayscale(0) sepia(1) hue-rotate(90deg) saturate(2);
    }
  }
}

.underPost {
  display: flex;
  flex-wrap: wrap;
}

#comments {
  flex: 5;
  padding-top: 0;
  margin-top: 0;
  &>:first-child {
    padding-top: 0;
    margin-top: 0;
  }
  &>:last-child {
    padding-bottom: 0;
    margin-bottom: 0;
  }
}

#sidebar {
  flex: 1;
  min-width: 12ch;
  background-color: #e4e4e4ff;
  text-align: center;
}

.comment {
  display: flex;
  flex-direction: column;
  margin: .2em 0 1em;
  &>* {
    box-sizing: border-box;
    // border: 2px dashed blue;
    // width: 100%;
    padding: .3em .5em;
  }
  &>:first-child {
    flex: 1;
    display: flex;
    align-items: center;
    gap: 2ch;
    // background-color: lightgrey;
    background-image: linear-gradient(10deg, #1114, transparent);
    border-radius: 8px;
    padding-top: .3em;
    padding-bottom: .3em;
    &>div {
      // border: 1px dashed green;
      display: flex;
      flex-direction: column;
    }
    img {
      border-radius: .55em;
      width: 2.75em;
      height: 2.75em;
      object-fit: cover;
    }
  }
}

@media only screen and (max-width: 900px) {
    #post, #comments, #sidebar {
    --paddingRL: 1.75em;
  }
}
@media only screen and (max-width: 500px) {
  nav {
    font-size: 20px;
  }

  .comment>:first-child img {
    display: none;
  }

  #computerMonitor {
    --borderSize: 10px;
  }
}

@media only screen and (max-width: 400px) {
  #post, #comments, #sidebar {
    --paddingRL: 1em;
  }
  #computerMonitor {
    --borderSize: 6px;
  }
}

@media only screen and (max-width: 310px) {
  #post, #comments, #sidebar {
    --paddingRL: 0em;
  }
}
</style>
