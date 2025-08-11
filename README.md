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
