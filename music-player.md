# music-player

## 概要

音楽プレイヤーを作成する

## 技術スタック

- React
- TypeScript
- Shadcn/ui
- TailwindCSS

## 環境

- Windows 11
- Node.js v22.14.0
- npm v10.9.2

## プロジェクト作成

```bash
npm create vite
npm install
npm run dev
```

起動確認
http://localhost:5173/

## TailwindCSS のインストール

参考：https://qiita.com/y_kato_eng/items/439dae65ba13d050c91e

- 以下コマンドを実行し、Tailswind CSS を使う上で必要なライブラリをインストールする。

```bash
npm install -D tailwindcss@3.4.13 postcss autoprefixer
```

- 以下コマンドを実行し、tailwind.config.js と postcss.config.js を作成する。

```bash
npx tailwindcss init -p
```

### [tailwind.config.js](tailwind.config.js) の「content」を以下のように修正

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

### 画面に読み込んでいる[index.css](./src/index.css) ファイルの内容を全削除し、以下の記述を追記

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### [App.tsx](./src/App.tsx) の return 内を全削除し、以下のように修正（TailwindCSS が効いているか確認）

```tsx
return (
  <>
    <div>
      <button className="bg-blue-500 hover:bg-blue-700">Button</button>
    </div>
  </>
);
```

ブラウザで表示を確認

## Shadcn/ui のインストール

参考：https://ui.shadcn.com/docs/installation/vite

### [tsconfig.json](tsconfig.json) の "compilerOptions" に以下を追加

```json
"compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
```

### TypeScript プロジェクトで Node.js の型定義をインストール

```bash
npm install -D @types/node
```

### [tsconfig.app.json](tsconfig.app.json) の "Linting" に以下を追加

```json
"baseUrl": ".",
"paths": {
  "@/*": ["./src/*"]
}
```

### [vite.config.ts](vite.config.ts) に以下を追加

```ts
import path from "path";

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "src"),
    },
  },
});
```

### Shadcn/ui のインストールコマンド

```bash
npx shadcn@latest init
```

### button コンポーネントのインストール

```bash
npx shadcn@latest add button
```

### button コンポーネントの確認

[app.tsx](./src/App.tsx) の return 内を全削除し、以下のように修正（button コンポーネント が効いているか確認）

```tsx
import { Button } from "@/components/ui/button";

return (
  <>
    <div>
      <Button>Button</Button>
    </div>
  </>
);
```

ブラウザで表示を確認（黒いボタンが表示されているか確認）

## 魔王魂のフリー音源の取得

参考：https://maou.audio/category/song/

## フリー音源で取得した曲の画像を検索して取得

## フリー音源と画像を [public](public) ディレクトリに配置

C:\Users\user\Documents\workspace\youtube\music-player\public

## ジャケットの切り替え（useState の理解）

[App.tsx](./src/App.tsx) へのリンク

```tsx
import { useRef, useState } from "react";

type Song = {
  title: string;
  artist: string;
  coverUrl: string;
  musicUrl: string;
};

const songs: Song[] = [
  {
    title: "シャイニングスター",
    artist: "詩歩",
    coverUrl: "public/shining_star.jpg",
    musicUrl: "shining_star.mp3",
  },
  {
    title: "Burning Heart",
    artist: "KEI",
    coverUrl: "public/burning_heart.jpg",
    musicUrl: "burning_heart.mp3",
  },
  {
    title: "12345",
    artist: "Mary",
    coverUrl: "public/12345.jpg",
    musicUrl: "12345.mp3",
  },
  {
    title: "ハルジオン",
    artist: "KEI",
    coverUrl: "public/halzion.jpg",
    musicUrl: "halzion.mp3",
  },
  {
    title: "Bipolar Disorder Outside ver.",
    artist: "森田交一",
    coverUrl: "public/outside.jpg",
    musicUrl: "outside.mp3",
  },
];

function App() {
  const [currentSongIndex, setCurrentSongIndex] = useState(0);

  const currentSong = songs[currentSongIndex];

  const handlePrevious = () => {
    setCurrentSongIndex(
      (prevIndex) => (prevIndex - 1 + songs.length) % songs.length
    );
    setIsPlaying(false);
  };

  const handleNext = () => {
    setCurrentSongIndex((prevIndex) => (prevIndex + 1) % songs.length);
    setIsPlaying(false);
  };
}

<Button
  variant="ghost"
  size="icon"
  onClick={handlePrevious}
  className="text-white hover:text-gray-300 transition-colors"
>
  <SkipBack className="h-6 w-6" />
</Button>;
<Button
  variant="ghost"
  size="icon"
  onClick={handleNext}
  className="text-white hover:text-gray-300 transition-colors"
>
  <SkipForward className="h-6 w-6" />
</Button>;
```

## 音楽の再生の制御（useRef の理解）

```tsx
import { useRef, useState } from "react";

const [isPlaying, setIsPlaying] = useState(false);
const audioRef = useRef<HTMLAudioElement>(null);

function App() {
  const [currentSongIndex, setCurrentSongIndex] = useState(0);

  const currentSong = songs[currentSongIndex];

  const handlePrevious = () => {
    setCurrentSongIndex(
      (prevIndex) => (prevIndex - 1 + songs.length) % songs.length
    );
    setIsPlaying(false);
  };

  const handleNext = () => {
    setCurrentSongIndex((prevIndex) => (prevIndex + 1) % songs.length);
    setIsPlaying(false);
  };

  const togglePlayPause = () => {
    if (!audioRef.current) return;

    if (isPlaying) {
      audioRef.current.pause();
    } else {
      audioRef.current.play();
    }
    setIsPlaying(!isPlaying);
  };
}

<Button
  variant="ghost"
  size="icon"
  onClick={togglePlayPause}
  className="text-white hover:text-gray-300 transition-colors"
>
  {isPlaying ? <Pause className="h-8 w-8" /> : <Play className="h-8 w-8" />}
</Button>

<audio ref={audioRef} src={currentSong.musicUrl} onEnded={handleNext} />;
```

### onEnded は、曲が終わったときに次の曲を再生するためのコールバック関数

```tsx
<audio ref={audioRef} src={currentSong.musicUrl} onEnded={handleNext} />
```

### 次の曲になったら、曲を止める

```tsx
setIsPlaying(false);
```
