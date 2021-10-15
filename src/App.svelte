<script>
  let playerEl
  let videoEl
  let progressEl
  let progressBarEl
  let toggleEl

  let mousedown = false

  function togglePlay() {
    const method = videoEl.paused ? 'play' : 'pause'
    videoEl[method]()
  }

  function updateButton() {
    toggleEl.textContent = this.paused ? '►' : '❚ ❚'
  }

  function skip() {
    videoEl.currentTime += parseFloat(this.dataset.skip)
  }

  function handleRangeUpdate() {
    videoEl[this.name] = this.value
  }

  function handleProgress() {
    const percent = (videoEl.currentTime / videoEl.duration) * 100
    progressBarEl.style.flexBasis = `${percent}%`
  }

  function scrub(e) {
    const scrubTime = (e.offsetX / progressEl.offsetWidth) * videoEl.duration
    videoEl.currentTime = scrubTime
  }

  function makeFullScreen() {
    if (videoEl.requestFullscreen) {
      videoEl.requestFullscreen()
    } else if (videoEl.webkitRequestFullscreen) {
      /* Safari */
      videoEl.webkitRequestFullscreen()
    } else if (videoEl.msRequestFullscreen) {
      /* IE11 */
      videoEl.msRequestFullscreen()
    }
  }
</script>

<svelte:head>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css"
  />
</svelte:head>

<div bind:this={playerEl} class="player">
  <video
    bind:this={videoEl}
    class="player__video viewer"
    src="652333414.mp4"
    on:click={togglePlay}
    on:play={updateButton}
    on:pause={updateButton}
    on:timeupdate={handleProgress}><track kind="captions" /></video
  >

  <div class="player__controls">
    <div
      bind:this={progressEl}
      class="progress"
      on:click={scrub}
      on:mousedown={() => (mousedown = true)}
      on:mouseup={() => (mousedown = false)}
      on:mousemove={e => mousedown && scrub(e)}
    >
      <div bind:this={progressBarEl} class="progress__filled" />
    </div>
    <button
      bind:this={toggleEl}
      class="player__button toggle"
      title="Toggle Play"
      on:click={togglePlay}>►</button
    >
    <input
      type="range"
      name="volume"
      class="player__slider"
      min="0"
      max="1"
      step="0.05"
      value="1"
      on:change={handleRangeUpdate}
    />
    <input
      type="range"
      name="playbackRate"
      class="player__slider"
      min="0.5"
      max="2"
      step="0.1"
      value="1"
      on:change={handleRangeUpdate}
    />
    <button on:click={skip} data-skip="-10" class="player__button">« 10s</button
    >
    <button on:click={skip} data-skip="25" class="player__button">25s »</button>
    <button on:click={makeFullScreen} class="player__button"
      ><i class="fas fa-expand" /></button
    >
  </div>
</div>

<style>
  .player {
    max-width: 750px;
    border: 5px solid rgba(0, 0, 0, 0.2);
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.2);
    position: relative;
    font-size: 0;
    overflow: hidden;
  }

  /* This css is only applied when fullscreen is active. */
  .player:fullscreen {
    max-width: none;
    width: 100%;
  }

  .player:-webkit-full-screen {
    max-width: none;
    width: 100%;
  }

  .player__video {
    width: 100%;
  }

  .player__button {
    background: none;
    border: 0;
    line-height: 1;
    color: white;
    text-align: center;
    outline: 0;
    padding: 0;
    cursor: pointer;
    max-width: 50px;
  }

  .player__button:focus {
    border-color: #ffc600;
  }

  .player__slider {
    width: 10px;
    height: 30px;
  }

  .player__controls {
    display: flex;
    position: absolute;
    bottom: 0;
    width: 100%;
    transform: translateY(100%) translateY(-5px);
    transition: all 0.3s;
    flex-wrap: wrap;
    background: rgba(0, 0, 0, 0.1);
  }

  .player:hover .player__controls {
    transform: translateY(0);
  }

  .player:hover .progress {
    height: 15px;
  }

  .player__controls > * {
    flex: 1;
  }

  .progress {
    flex: 10;
    position: relative;
    display: flex;
    flex-basis: 100%;
    height: 5px;
    transition: height 0.3s;
    background: rgba(0, 0, 0, 0.5);
    cursor: ew-resize;
  }

  .progress__filled {
    width: 50%;
    background: #ffc600;
    flex: 0;
    flex-basis: 0%;
  }

  /* unholy css to style input type="range" */

  input[type='range'] {
    -webkit-appearance: none;
    background: transparent;
    width: 100%;
    margin: 0 5px;
  }

  input[type='range']:focus {
    outline: none;
  }

  input[type='range']::-webkit-slider-runnable-track {
    width: 100%;
    height: 8.4px;
    cursor: pointer;
    box-shadow: 1px 1px 1px rgba(0, 0, 0, 0), 0 0 1px rgba(13, 13, 13, 0);
    background: rgba(255, 255, 255, 0.8);
    border-radius: 1.3px;
    border: 0.2px solid rgba(1, 1, 1, 0);
  }

  input[type='range']::-webkit-slider-thumb {
    height: 15px;
    width: 15px;
    border-radius: 50px;
    background: #ffc600;
    cursor: pointer;
    -webkit-appearance: none;
    margin-top: -3.5px;
    box-shadow: 0 0 2px rgba(0, 0, 0, 0.2);
  }

  input[type='range']:focus::-webkit-slider-runnable-track {
    background: #bada55;
  }

  input[type='range']::-moz-range-track {
    width: 100%;
    height: 8.4px;
    cursor: pointer;
    box-shadow: 1px 1px 1px rgba(0, 0, 0, 0), 0 0 1px rgba(13, 13, 13, 0);
    background: #ffffff;
    border-radius: 1.3px;
    border: 0.2px solid rgba(1, 1, 1, 0);
  }

  input[type='range']::-moz-range-thumb {
    box-shadow: 0 0 0 rgba(0, 0, 0, 0), 0 0 0 rgba(13, 13, 13, 0);
    height: 15px;
    width: 15px;
    border-radius: 50px;
    background: #ffc600;
    cursor: pointer;
  }
</style>
