<!DOCTYPE html>
<html lang="en">
<head>
  <!-- (unchanged head: meta, fonts, styles) -->
  <style>
    /* small addition: skip link styles */
    .skip-link{
      position:fixed;
      left:12px;
      top:12px;
      background:var(--paper-light);
      color:var(--ink);
      padding:8px 12px;
      border:3px solid var(--ink);
      border-radius:6px;
      transform:translateY(-120%);
      transition:transform 0.12s ease;
      z-index:9999;
      font-family:'Space Mono', monospace;
      text-decoration:none;
    }
    .skip-link:focus{ transform:translateY(0); outline:3px solid var(--marigold); }
  </style>
</head>
<body>

<!-- accessibility: skip link -->
<a href="#main-content" class="skip-link">Skip to content</a>

<header class="hero">
  <p class="eyebrow">Neighbourhood Comic &amp; Book Shop</p>
  <h1 class="shop-name display">NANDHINI<br>BOOKS</h1>
  <p class="tagline">Comics, stories and good reads — opposite Molachur Arch, Sunguvarchatram.</p>
  <div class="status-row">
    <!-- updated status: role + aria-live, dot hidden from AT -->
    <span class="status-pill mono" id="status-pill" role="status" aria-live="polite" aria-atomic="true">
      <span class="dot" aria-hidden="true"></span>
      <span id="status-text">Checking hours…</span>
    </span>
    <span class="hours-chip mono">8:30 AM – 5:00 PM</span>
  </div>
  <div class="starburst" aria-hidden="true"><span class="display">FROM<br>₹100</span></div>
</header>

<main id="main-content">
  <!-- rest of page unchanged -->
</main>

<script>
  // Improved live open/closed status with relative time and periodic updates
  (function () {
    var pill = document.getElementById('status-pill');
    var text = document.getElementById('status-text');

    // Business hours: change here if needed. Using minutes since midnight.
    var openMinutes = 8 * 60 + 30;   // 8:30 AM
    var closeMinutes = 17 * 60;      // 5:00 PM

    function plural(n, label) {
      return n + ' ' + label + (n === 1 ? '' : 's');
    }

    function formatTimeFromMinutes(mins) {
      var d = new Date();
      d.setHours(0, 0, 0, 0);
      d.setMinutes(mins);
      // locale-aware short time (e.g., "5:00 PM")
      return d.toLocaleTimeString([], { hour: 'numeric', minute: '2-digit' });
    }

    function minutesUntil(targetMinutes, nowMinutes) {
      var diff = targetMinutes - nowMinutes;
      if (diff < 0) diff += 24 * 60; // next day
      return diff;
    }

    function humanDuration(totalMinutes) {
      var h = Math.floor(totalMinutes / 60);
      var m = totalMinutes % 60;
      if (h > 0 && m > 0) return plural(h, 'hour') + ' ' + plural(m, 'minute');
      if (h > 0) return plural(h, 'hour');
      return plural(m, 'minute');
    }

    function updateStatus() {
      var now = new Date();
      var nowMinutes = now.getHours() * 60 + now.getMinutes();

      // Check if currently open (open inclusive, close exclusive)
      var isOpen = (nowMinutes >= openMinutes && nowMinutes < closeMinutes);

      if (isOpen) {
        var minsLeft = closeMinutes - nowMinutes;
        pill.classList.remove('closed');
        text.textContent = 'Open — closes at ' + formatTimeFromMinutes(closeMinutes) + ' (' + humanDuration(minsLeft) + ' left)';
      } else {
        var untilOpen = minutesUntil(openMinutes, nowMinutes);
        // If open time has already passed today, phrase as "tomorrow" when needed
        var opensAt = formatTimeFromMinutes(openMinutes);
        var when = (nowMinutes < openMinutes) ? 'today' : 'tomorrow';
        pill.classList.add('closed');
        text.textContent = 'Closed — opens ' + when + ' at ' + opensAt + ' (in ' + humanDuration(untilOpen) + ')';
      }
    }

    // Initial update and periodic refresh every 30 seconds (fine for minute granularity)
    updateStatus();
    // Sync to the next minute boundary for accurate updates
    var now = new Date();
    var msUntilNextMinute = (60 - now.getSeconds()) * 1000 + 50;
    setTimeout(function () {
      updateStatus();
      setInterval(updateStatus, 30 * 1000);
    }, msUntilNextMinute);
  })();
</script>

</body>
</html>
