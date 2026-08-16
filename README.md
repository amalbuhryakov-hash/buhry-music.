# buhry-music.
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>BuHry Music</title>

<style>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    font-family: Arial, sans-serif;
    background: #08090d;
    color: white;
    min-height: 100vh;
}

header {
    padding: 25px 20px;
    text-align: center;
    background: #11131a;
    border-bottom: 1px solid #252832;
}

.logo {
    font-size: 30px;
    font-weight: bold;
}

.logo span {
    color: #7c5cff;
}

.subtitle {
    margin-top: 7px;
    color: #8d93a1;
    font-size: 14px;
}

.container {
    padding: 20px;
    max-width: 700px;
    margin: auto;
}

.search {
    width: 100%;
    padding: 15px;
    border-radius: 14px;
    border: 1px solid #30333d;
    background: #171922;
    color: white;
    font-size: 16px;
    outline: none;
    margin-bottom: 15px;
}

.add-button {
    width: 100%;
    padding: 15px;
    border: none;
    border-radius: 14px;
    background: #7c5cff;
    color: white;
    font-size: 16px;
    font-weight: bold;
    cursor: pointer;
    margin-bottom: 20px;
}

.add-button:active {
    transform: scale(0.98);
}

input[type="file"] {
    display: none;
}

.playlist {
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.song {
    background: #151720;
    border: 1px solid #292c36;
    border-radius: 15px;
    padding: 15px;
    display: flex;
    align-items: center;
    gap: 14px;
    cursor: pointer;
}

.song.active {
    border-color: #7c5cff;
    background: #1b1929;
}

.cover {
    width: 48px;
    height: 48px;
    border-radius: 12px;
    background: linear-gradient(135deg, #7c5cff, #b05cff);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 22px;
    flex-shrink: 0;
}

.song-info {
    min-width: 0;
    flex: 1;
}

.song-title {
    font-size: 16px;
    font-weight: bold;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

.song-subtitle {
    margin-top: 5px;
    color: #858b99;
    font-size: 13px;
}

.empty {
    text-align: center;
    padding: 45px 20px;
    color: #858b99;
}

.player {
    position: fixed;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(17,19,26,0.97);
    border-top: 1px solid #292c36;
    padding: 15px 20px 20px;
    backdrop-filter: blur(15px);
}

.player-inner {
    max-width: 700px;
    margin: auto;
}

.now-playing {
    text-align: center;
    margin-bottom: 10px;
}

.now-title {
    font-weight: bold;
    font-size: 15px;
}

.now-status {
    color: #858b99;
    font-size: 12px;
    margin-top: 4px;
}

.progress {
    width: 100%;
    margin: 5px 0 10px;
    accent-color: #7c5cff;
}

.controls {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 18px;
}

.control {
    width: 45px;
    height: 45px;
    border-radius: 50%;
    border: none;
    background: #242732;
    color: white;
    font-size: 18px;
    cursor: pointer;
}

.play {
    width: 58px;
    height: 58px;
    background: #7c5cff;
    font-size: 23px;
}

.volume {
    margin-top: 10px;
    width: 100%;
    accent-color: #7c5cff;
}

.spacer {
    height: 190px;
}
</style>
</head>

<body>

<header>
    <div class="logo">🎧 <span>BuHry</span> Music</div>
    <div class="subtitle">Твоя музыка. В одном месте.</div>
</header>

<div class="container">

    <input
        id="search"
        class="search"
        type="text"
        placeholder="🔎 Найти песню..."
    >

    <label class="add-button">
        ➕ Добавить музыку
        <input
            id="fileInput"
            type="file"
            accept="audio/*"
            multiple
        >
    </label>

    <div id="playlist" class="playlist">
        <div class="empty">
            <div style="font-size:45px;">🎵</div>
            <br>
            Здесь пока нет песен
            <br><br>
            Нажми «Добавить музыку» и выбери песни.
        </div>
    </div>

    <div class="spacer"></div>

</div>

<div class="player">

    <div class="player-inner">

        <div class="now-playing">
            <div id="nowTitle" class="now-title">
                Ничего не играет
            </div>

            <div id="nowStatus" class="now-status">
                Выбери песню
            </div>
        </div>

        <input
            id="progress"
            class="progress"
            type="range"
            min="0"
            max="100"
            value="0"
        >

        <div class="controls">

            <button class="control" id="prev">
                ⏮
            </button>

            <button class="control play" id="play">
                ▶
            </button>

            <button class="control" id="next">
                ⏭
            </button>

        </div>

        <input
            id="volume"
            class="volume"
            type="range"
            min="0"
            max="1"
            step="0.01"
            value="1"
        >

    </div>

</div>

<audio id="audio"></audio>

<script>

const fileInput = document.getElementById("fileInput");
const playlist = document.getElementById("playlist");
const audio = document.getElementById("audio");

const playButton = document.getElementById("play");
const prevButton = document.getElementById("prev");
const nextButton = document.getElementById("next");

const progress = document.getElementById("progress");
const volume = document.getElementById("volume");

const nowTitle = document.getElementById("nowTitle");
const nowStatus = document.getElementById("nowStatus");

const search = document.getElementById("search");

let songs = [];
let currentIndex = -1;


/* Добавление файлов */

fileInput.addEventListener("change", function() {

    const files = Array.from(this.files);

    files.forEach(file => {

        songs.push({
            name: file.name,
            url: URL.createObjectURL(file)
        });

    });

    renderPlaylist();

    if (currentIndex === -1 && songs.length > 0) {
        loadSong(0);
    }

});


/* Отображение списка */

function renderPlaylist(filter = "") {

    playlist.innerHTML = "";

    const filteredSongs = songs.filter(song =>
        song.name.toLowerCase().includes(filter.toLowerCase())
    );

    if (filteredSongs.length === 0) {

        playlist.innerHTML = `
            <div class="empty">
                🎵<br><br>
                Песни не найдены
            </div>
        `;

        return;
    }

    filteredSongs.forEach(song => {

        const index = songs.indexOf(song);

        const item = document.createElement("div");

        item.className = "song";

        if (index === currentIndex) {
            item.classList.add("active");
        }

        item.innerHTML = `
            <div class="cover">♫</div>

            <div class="song-info">

                <div class="song-title">
                    ${escapeHtml(song.name)}
                </div>

                <div class="song-subtitle">
                    BuHry Music
                </div>

            </div>

            <div>
                ${index === currentIndex && !audio.paused ? "🔊" : "▶"}
            </div>
        `;

        item.addEventListener("click", () => {

            loadSong(index);
            audio.play();

        });

        playlist.appendChild(item);

    });

}


/* Загрузка песни */

function loadSong(index) {

    if (index < 0 || index >= songs.length) return;

    currentIndex = index;

    audio.src = songs[index].url;

    nowTitle.textContent = songs[index].name;
    nowStatus.textContent = "BuHry Music";

    progress.value = 0;

    renderPlaylist(search.value);

}


/* Play / Pause */

playButton.addEventListener("click", () => {

    if (songs.length === 0) {
        alert("Сначала добавь музыку.");
        return;
    }

    if (currentIndex === -1) {
        loadSong(0);
    }

    if (audio.paused) {

        audio.play();

    } else {

        audio.pause();

    }

});


audio.addEventListener("play", () => {

    playButton.textContent = "⏸";

    nowStatus.textContent = "Сейчас играет";

    renderPlaylist(search.value);

});


audio.addEventListener("pause", () => {

    playButton.textContent = "▶";

    nowStatus.textContent = "Пауза";

    renderPlaylist(search.value);

});


/* Следующая */

nextButton.addEventListener("click", () => {

    if (songs.length === 0) return;

    let next = currentIndex + 1;

    if (next >= songs.length) {
        next = 0;
    }

    loadSong(next);
    audio.play();

});


/* Предыдущая */

prevButton.addEventListener("click", () => {

    if (songs.length === 0) return;

    let previous = currentIndex - 1;

    if (previous < 0) {
        previous = songs.length - 1;
    }

    loadSong(previous);
    audio.play();

});


/* Автоматически следующая песня */

audio.addEventListener("ended", () => {

    if (songs.length === 0) return;

    let next = currentIndex + 1;

    if (next >= songs.length) {
        next = 0;
    }

    loadSong(next);
    audio.play();

});


/* Прогресс */

audio.addEventListener("timeupdate", () => {

    if (!audio.duration) return;

    progress.value =
        (audio.currentTime / audio.duration) * 100;

});


progress.addEventListener("input", () => {

    if (!audio.duration) return;

    audio.currentTime =
        (progress.value / 100) * audio.duration;

});


/* Громкость */

volume.addEventListener("input", () => {

    audio.volume = volume.value;

});


/* Поиск */

search.addEventListener("input", () => {

    renderPlaylist(this.value);

});


/* Безопасный вывод названия файла */

function escapeHtml(text) {

    const div = document.createElement("div");

    div.textContent = text;

    return div.innerHTML;

}

</script>

</body>
</html>
