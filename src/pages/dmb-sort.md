---
layout: ../layouts/Layout.astro
title: DMB Song Sorter
---

<style>
    .song-button {
        width: 300px;
        font-family: 'BerkeleyMono', monospace;
        padding: 3rem;
        margin: 1rem;
    }
</style>

<div>
  <div id="comparison" style="display:none;">
    <button class="song-button" id="choice1"></button>
    <button class="song-button" id="choice2"></button>
  </div>
  <p id="progress">Loading...</p>
  <pre id="result" style="display:none;"></pre>
</div>

<script>
const songs = {
  "Remember Two Things": [
    "Recently",
    "One Sweet World",
    "The Song That Jane Likes",
    "Minarets",
    "Seek Up",
    "I'll Back You Up",
    "Christmas Song",
  ],

  "Under the Table and Dreaming": [
    "The Best of What's Around",
    "What Would You Say",
    "Satellite",
    "Rhyme & Reason",
    "Typical Situation",
    "Dancing Nancies",
    "Ants Marching",
    "Lover Lay Down",
    "Jimi Thing",
    "Warehouse",
    "Pay for What You Get",
    "#34",
  ],

  Crash: [
    "So Much to Say",
    "Two Step",
    "Crash Into Me",
    "Too Much",
    "#41",
    "Say Goodbye",
    "Drive In Drive Out",
    "Let You Down",
    "Lie in Our Graves",
    "Cry Freedom",
    "Tripping Billies",
    "Proudest Monkey",
  ],

  "Before These Crowded Streets": [
    "Pantala Naga Pampa",
    "Rapunzel",
    "The Last Stop",
    "Don't Drink the Water",
    "Stay (Wasting Time)",
    "Halloween",
    "The Stone",
    "Crush",
    "The Dreaming Tree",
    "Pig",
    "Spoon",
  ],

  Everyday: [
    "I Did It",
    "When the World Ends",
    "The Space Between",
    "Dreams of Our Fathers",
    "So Right",
    "If I Had It All",
    "What You Are",
    "Angel",
    "Fool to Think",
    "Sleep to Dream Her",
    "Mother Father",
    "Everyday",
  ],

  "Busted Stuff": [
    "Busted Stuff",
    "Grey Street",
    "Where Are You Going",
    "You Never Know",
    "Captain",
    "Raven",
    "Grace Is Gone",
    "Kit Kat Jam",
    "Digging a Ditch",
    "Big Eyed Fish",
    "Bartender",
  ],

  "Some Devil": [
    "Dodo",
    "So Damn Lucky",
    "Gravedigger",
    "Some Devil",
    "Trouble",
    "Grey Blue Eyes",
    "Save Me",
    "Stay or Leave",
    "An' Another Thing",
    "Oh",
    "Baby",
    "Up and Away",
    "Too High",
  ],

  "Stand Up": [
    "Dreamgirl",
    "Old Dirt Hill (Bring That Beat Back)",
    "Stand Up (For It)",
    "American Baby Intro",
    "American Baby",
    "Smooth Rider",
    "Everybody Wake Up (Our Finest Hour Arrives)",
    "Out of My Hands",
    "Hello Again",
    "Louisiana Bayou",
    "Stolen Away on 55th & 3rd",
    "You Might Die Trying",
    "Steady As We Go",
    "Hunger for the Great Light",
  ],

  "Big Whiskey & the GrooGrux King": [
    "Grux",
    "Shake Me Like a Monkey",
    "Funny the Way It Is",
    "Lying in the Hands of God",
    "Why I Am",
    "Dive In",
    "Spaceman",
    "Squirm",
    "Alligator Pie",
    "Seven",
    "Time Bomb",
    "Baby Blue",
    "You & Me",
  ],

  "Away from the World": [
    "Broken Things",
    "Belly Belly Nice",
    "Mercy",
    "Gaucho",
    "Sweet",
    "The Riff",
    "Belly Full",
    "If Only",
    "Rooftop",
    "Snow Outside",
    "Drunken Soldier",
  ],

  "Come Tomorrow": [
    "Samurai Cop (Oh Joy Begin)",
    "Can't Stop",
    "Here On Out",
    "That Girl Is You",
    "She",
    "Idea of You",
    "Virginia in the Rain",
    "Again and Again",
    "bkdkdkdd",
    "Black and Blue Bird",
    "Come On Come On",
    "Do You Remember",
    "Come Tomorrow",
    "When I'm Weary",
  ],

  "Walk Around the Moon": [
    "Walk Around the Moon",
    "Madman's Eyes",
    "Looking for a Vein",
    "The Ocean and the Butterfly",
    "It Could Happen",
    "Something to Tell My Baby",
    "After Everything",
    "All You Wanted Was Tomorrow",
    "The Only Thing",
    "Break Free",
    "Monsters",
    "Singing From the Windows",
  ],

  Unreleased: [
    "#40",
    "Bismarck",
    "Blackjack",
    "Blue Water",
    "Break for It",
    "Cha Cha",
    "Cigarette Lit",
    "Crazy Easy",
    "Death on the High Seas",
    "Deed Is Done",
    "A Dream So Real",
    "Dreamed I Killed God",
    "Falling Off the Roof",
    "Get in Line",
    "Good Good Time",
    "Heathcliff's Haiku Warriors",
    "Improv/Jam",
    "JTR",
    "Kill the King",
    "Light Lift Me Up",
    "Little Thing",
    "Loving Wings",
    "Monkey Man",
    "Once on a Wild Afternoon",
    "People People",
    "Plastic Girl",
    "Shotgun",
    "Sister",
    "Spotlight",
    "Straight Shot",
    "Sugar Will",
    "Sweet Up and Down",
  ],

  Misc: [
    "#27",
    "Anyone Seen The Bridge",
    "Beach Ball",
    "Corn Bread",
    "Eh Hee",
    "Granny",
    "Help Myself",
    "Joyride",
    "Kill the Preacher",
    "Little Red Bird",
    "New Song",
    "Trouble With You",
    "Water Into Wine",
    "What Will Become of Me",
    "Write a Song",
  ],
};

let allSongs = Object.values(songs).flat();
let comparisonCount = 0;
let totalComparisons = 0;

// Estimate comparisons like merge sort
function countComparisons(n) {
  if (n <= 1) return 0;
  const mid = Math.floor(n / 2);
  return countComparisons(mid) + countComparisons(n - mid) + n - 1;
}

totalComparisons = countComparisons(allSongs.length);

document.getElementById("progress").innerText = `0 / ${totalComparisons} comparisons`;

let comparisonIndex = 0;

async function interactiveSort(list) {
  if (list.length <= 1) return list;

  const mid = Math.floor(list.length / 2);
  const left = await interactiveSort(list.slice(0, mid));
  const right = await interactiveSort(list.slice(mid));
  return await interactiveMerge(left, right);
}

function showComparison(a, b) {
  return new Promise((resolve) => {
    document.getElementById("comparison").style.display = "block";
    document.getElementById("choice1").innerText = a;
    document.getElementById("choice2").innerText = b;

    const handler = (choice) => {
      comparisonCount++;
      document.getElementById("progress").innerText = `${comparisonCount} / ${totalComparisons} comparisons`;
      document.getElementById("choice1").onclick = null;
      document.getElementById("choice2").onclick = null;
      resolve(choice === 1 ? true : false);
    };

    document.getElementById("choice1").onclick = () => handler(1);
    document.getElementById("choice2").onclick = () => handler(2);
  });
}

async function interactiveMerge(left, right) {
  let result = [];
  let i = 0, j = 0;

  while (i < left.length && j < right.length) {
    const preferLeft = await showComparison(left[i], right[j]);
    if (preferLeft) {
      result.push(left[i++]);
    } else {
      result.push(right[j++]);
    }
  }

  return result.concat(left.slice(i)).concat(right.slice(j));
}

function encodeOrder(arr) {
  const uint16 = new Uint16Array(arr);
  let binary = '';
  for (let i = 0; i < uint16.length; i++) {
    binary += String.fromCharCode(uint16[i]);
  }
  return btoa(binary).replace(/\+/g, '-').replace(/\//g, '_').replace(/=+$/, '').toLowerCase();
}

function decodeOrder(str) {
  str = str.toUpperCase().replace(/-/g, '+').replace(/_/g, '/');
  while (str.length % 4) {
    str += '=';
  }
  const binary = atob(str);
  const arr = new Uint16Array(binary.length);
  for (let i = 0; i < binary.length; i++) {
    arr[i] = binary.charCodeAt(i);
  }
  return Array.from(arr);
}

function getOrderFromUrl() {
  const params = new URLSearchParams(window.location.search);
  return params.get('order');
}

function updateUrlWithOrder(orderStr) {
  const url = new URL(window.location);
  url.searchParams.set('order', orderStr);
  window.history.replaceState({}, '', url);
}

(async () => {
  const orderStr = getOrderFromUrl();

  if (orderStr) {
    // If order is in URL, decode and show results immediately
    const orderIndices = decodeOrder(orderStr);
    const sorted = orderIndices.map(i => allSongs[i]).filter(Boolean);

    document.getElementById("comparison").style.display = "none";
    document.getElementById("progress").innerText = "";
    const resultEl = document.getElementById("result");
    resultEl.style.display = "block";
    const padLength = String(sorted.length).length;
    resultEl.innerText = sorted
      .map((s, i) => `${String(i + 1).padStart(padLength, '0')}. ${s}`)
      .join("\n");
  } else {
    // No order param, do interactive sort
    const sorted = await interactiveSort(allSongs);
    document.getElementById("comparison").innerText = "";
    document.getElementById("progress").innerText = "";

    const resultEl = document.getElementById("result");
    resultEl.style.display = "block";
    resultEl.innerText = sorted.map((s, i) => `${i + 1}. ${s}`).join("\n");

    // Encode sorted order and update URL
    const orderIndices = sorted.map(song => allSongs.indexOf(song));
    const encoded = encodeOrder(orderIndices);
    updateUrlWithOrder(encoded);
  }
})();
