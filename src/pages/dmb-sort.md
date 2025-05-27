---
layout: ../layouts/Layout.astro
title: DMB Song Sorter
---

<style>
    .song-button {
        border: 3px solid black;
        font-family: 'BerkeleyMono', monospace;
        font-weight: 800;
        margin: 1rem;
        overflow-wrap: break-word;
        padding: 3rem 0;
        text-align: center;
        white-space: normal;
        width: 90%;
    }
    @media (min-width: 768px) {
    .song-button {
        width: 300px;
    }
}
</style>

<div>
  <div id="comparison" style="display:none;">
    <div class="song-button" id="choice1"></div>
    <div class="song-button" id="choice2"></div>
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
    "Trouble With You",
    "Water Into Wine",
    "What Will Become of Me",
    "Write a Song",
  ],
};

function* sort(arr) {
  if (arr.length <= 1) return arr;

  const mid = Math.floor(arr.length / 2);
  const left = yield* sort(arr.slice(0, mid));
  const right = yield* sort (arr.slice(mid));

  const merged = [];
  let i = 0, j = 0;
  while (i < left.length && j < right.length) {
    const a = left[i];
    const b = right[j];
    const prefersA = yield [a, b];
    if (prefersA) {
      merged.push(a);
      i++;
    } else {
      merged.push(b);
      j++;
    }
  }

  return merged.concat(left.slice(i)).concat(right.slice(j));
}

function countComparisons(n) {
  if (n <= 1) return 0;
  const mid = Math.floor(n / 2);
  return countComparisons(mid) + countComparisons(n - mid) + n - 1;
}

(() => {
  const allSongs = Object.values(songs).flat();
  const totalComparisons = countComparisons(allSongs.length);

  const sorter = sort(allSongs);
  let state = sorter.next();
  let comparisonsDone = 0;

  function updateProgress() {
    document.getElementById("progress").innerText = `${comparisonsDone} / ${totalComparisons} comparisons`;
  }

  function updateUI() {
    const comparisonDiv = document.getElementById("comparison");
    const choice1 = document.getElementById("choice1");
    const choice2 = document.getElementById("choice2");
    const progressEl = document.getElementById("progress");
    const resultEl = document.getElementById("result");

    updateProgress();
  
    if (state.done) {
      comparisonDiv.style.display = "none";
      resultEl.style.display = "block";
      progressEl.innerText = "";
      resultEl.innerText = state.value.map((song, i) => `${i + 1}. ${song}`).join("\n");
      return;
    }
  
    const [a, b] = state.value;
  
    comparisonDiv.style.display = "block";
    resultEl.style.display = "none";
  
    function onClick(value) {
      comparisonsDone++;
      state = sorter.next(value);
      updateUI();
    };
  
    choice1.innerText = a;
    choice1.onclick = () => onClick(true);
    choice2.innerText = b;
    choice2.onclick = () => onClick(false);
  }
  updateUI();
})();
