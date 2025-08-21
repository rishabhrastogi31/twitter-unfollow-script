# twitter-unfollow-script
#twitter auto
(async function fastUnfollow() {
  const DELAY_MS = 500; // delay between actions

  function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

  function isVisible(el) {
    if (!el) return false;
    const r = el.getBoundingClientRect();
    const cs = window.getComputedStyle(el);
    return r.width > 0 && r.height > 0 && cs.visibility !== 'hidden' && cs.display !== 'none';
  }

  function findFollowingButton() {
    return Array.from(document.querySelectorAll('div[role="button"], button'))
      .find(el => isVisible(el) && /^Following$/i.test((el.innerText || '').trim()));
  }

  async function clickConfirm() {
    await sleep(200);
    const btn = Array.from(document.querySelectorAll('div[role="button"], button'))
      .find(el => isVisible(el) && /^Unfollow$/i.test((el.innerText || '').trim()));
    if (btn) {
      btn.click();
      return true;
    }
    return false;
  }

  let count = 0;
  while (true) {
    const btn = findFollowingButton();
    if (!btn) {
      window.scrollBy(0, 1000);
      await sleep(500);
      continue;
    }
    btn.scrollIntoView({ behavior: 'smooth', block: 'center' });
    btn.click();
    await clickConfirm();
    count++;
    console.log(`Unfollowed ${count}`);
    await sleep(DELAY_MS);
  }
})();


twitter auto scroll script [ it will stop on pressing enter]
(() => {
  // Clean up any previous run
  if (window.__xAutoScrollStop) {
    window.__xAutoScrollStop();
  }

  const SCROLL_SPEED_PX_PER_MS = 5; // ~5000 px/sec. Increase for faster.
  let running = true;
  let last = performance.now();

  const isEditable = el =>
    !el ? false :
    el.isContentEditable ||
    ['INPUT','TEXTAREA','SELECT'].includes(el.tagName);

  const onKeyDown = (e) => {
    // Stop on Enter or Escape, but ignore if you're typing in an input/textarea/composer
    if ((e.key === 'Enter' || e.key === 'Escape') && !isEditable(e.target)) {
      running = false;
      cleanup();
      console.log('[AutoScroll] Stopped.');
    }
  };

  const step = (ts) => {
    if (!running) return;
    const dt = ts - last;
    last = ts;
    window.scrollBy(0, dt * SCROLL_SPEED_PX_PER_MS);
    requestAnimationFrame(step);
  };

  const cleanup = () => {
    document.removeEventListener('keydown', onKeyDown, true);
    delete window.__xAutoScrollStop;
  };

  document.addEventListener('keydown', onKeyDown, true);
  window.__xAutoScrollStop = () => { running = false; cleanup(); };

  console.log('[AutoScroll] Running… Press Enter or Esc (not while typing) to stop.');
  requestAnimationFrame(step);
})();

