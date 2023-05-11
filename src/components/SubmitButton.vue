<template>
  <button class="submit-btn">
    <span class="text"><slot></slot></span>
    <span class="marquee" aria-hidden><slot></slot></span>
  </button>
</template>
<style lang="scss" scoped>
$redTeamColor: rgb(var(--red-4));
$blueTeamColor: rgb(var(--arcoblue-4));
.submit-btn,
.submit-btn *,
.submit-btn :after,
.submit-btn :before,
.submit-btn:after,
.submit-btn:before {
  border: 0 solid;
  box-sizing: border-box;
}
.submit-btn {
  -webkit-tap-highlight-color: transparent;
  -webkit-appearance: button;
  appearance: button;
  background-image: none;
  color: #fff;
  cursor: pointer;
  font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont,
    Segoe UI, Roboto, Helvetica Neue, Arial, Noto Sans, sans-serif,
    Apple Color Emoji, Segoe UI Emoji, Segoe UI Symbol, Noto Color Emoji;
  font-size: 100%;
  font-weight: 900;
  line-height: 1.5;
  margin: 0;
  padding: 0;
  text-transform: uppercase;

  &.red {
    background-color: $redTeamColor;
  }

  &.blue {
    background-color: $blueTeamColor;
  }
}
.submit-btn:disabled {
  cursor: default;
}
.submit-btn:-moz-focusring {
  outline: auto;
}
.submit-btn svg {
  display: block;
  vertical-align: middle;
}
.submit-btn [hidden] {
  display: none;
}
.submit-btn {
  border-radius: 99rem;
  border-width: 2px;
  overflow: hidden;
  padding: 0.8rem 3rem;
  position: relative;
  box-shadow: 0 0 0 2em transparent;

  -webkit-animation: pulse 2s infinite;
  animation: pulse 2s infinite;
}
.submit-btn span {
  display: grid;
  inset: 0;
  place-items: center;
  position: absolute;
  transition: opacity 0.2s ease;
}
.submit-btn .marquee {
  --spacing: 5em;
  --start: 0em;
  --end: 5em;
  -webkit-animation: marquee 1s linear infinite;
  animation: marquee 1s linear infinite;
  -webkit-animation-play-state: paused;
  animation-play-state: paused;
  opacity: 0;
  position: relative;
  text-shadow: #fff var(--spacing) 0, #fff calc(var(--spacing) * -1) 0,
    #fff calc(var(--spacing) * -2) 0;
}
.submit-btn:hover .marquee {
  -webkit-animation-play-state: running;
  animation-play-state: running;
  opacity: 1;
}
.submit-btn:hover .text {
  opacity: 0;
}
@-webkit-keyframes marquee {
  0% {
    transform: translateX(var(--start));
  }
  to {
    transform: translateX(var(--end));
  }
}
@keyframes marquee {
  0% {
    transform: translateX(var(--start));
  }
  to {
    transform: translateX(var(--end));
  }
}

@-webkit-keyframes pulse {
  0% {
    box-shadow: 0 0 0 0 #fff;
  }
}
@keyframes pulse {
  0% {
    box-shadow: 0 0 0 0 #fff;
  }
}
</style>
