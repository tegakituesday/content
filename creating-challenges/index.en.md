---
title: Creating Challenges
description: "A guide to authoring Tegaki Tuesday community challenges: turning Japanese lyrics, furigana, dictionary entries, and media into a submittable prompt."
nav: true
sort: 30
author: 441283734214279178 # elnudev
updated: 2026-06-17
---

{{< toc >}}

In addition to the weekly official Tegaki Tuesday challenges, you can also create your own community challenges to submit to!

To create a challenge, use the [Wizard](/new) to automatically format for you, then do a quick pass to make sure that it has inferred the correct furigana and dictionary link choices. While the Wizard is smart, it still has to guess readings from context (for instance, is 空 read [そら](https://jisho.org/word/空-1) or [から](https://jisho.org/word/空)?) and it will completely miss more complex literary devices such as [*gikun* [義]{ぎ}[訓]{くん}](https://morg.systems/Gikun).

- Note that in order to use the Wizard, you must be both a member of [a participating Discord server](/about#how-do-i-submit) and have submitted at least once.
- Admins may edit your challenges to correct mistakes.
- Before publishing your challenge, be sure to edit the slug (the name at the top of the page) so it is something identifiable rather than the default three random words. Once someone submits to your challenge, this is no longer editable.

## The Wizard

Click on the [<svg class="icon"><use xlink:href="#document_plus" /></svg> New challenge](/new) link in the navbar to the left of your profile picture, paste in your Japanese lyrics, fill in a few details about the song (its title with furigana[^furigana] and a link to the music), and let it run.

Behind the scenes, the Wizard reads through your lyrics and does the tedious parts for you: it splits the passage into individual words, figures out the reading (furigana) for each one, tags what kind of word it is, and links each word to its dictionary entry on [Jisho.org](https://jisho.org/). When it finishes, you get a draft challenge you can look over, fix up if needed, and publish.

## Finding an English translation

**Do not use machine translation.** If you don't want to translate the challenge yourself, one of the following sites is probably your best bet:

- [Vocaloid Lyrics Wiki](https://vocaloidlyrics.miraheze.org/) for Vocaloid songs
- [Lyrics Translate](https://lyricstranslate.com/) for everything else

Be sure to add a translation credit. If you didn't fill out the translation section of the Wizard, you can still add one later by manually adding the ```yaml translation:``` key (see [Translation](#translation) below).


## Writing a challenge by hand

When you create a challenge, what you're really editing is a block of text in a format called [YAML](https://yaml.org/) according to the challenge schema. Once you see a couple examples, you'll see it's really quite simple.

Beyond the example in this tutorial, you can view the raw formatting for any challenge on the site by clicking on the <svg class="icon"><use xlink:href="#code_bracket" /></svg> icon next to the challenge content if you're wondering how to do something specific.

To demonstrate, we will be looking at the formatting of the first two lines of [Tegaki Tuesday #85 — [祭]{さい}[果]{は}ての[花]{はな}](/85/source):

> [手]{て}[鏡]{かがみ}に[映]{うつ}した　[今]{いま}は[昔]{むか}の[面]{おも}[影]{かげ} \
> [君]{きみ}を[想]{おも}いながら　「[花]{はな}」を[探]{さが}す[旅]{たび}[路]{じ}
>
> *A vestige of once upon a time, being reflected in the hand mirror* \
> *Journey to find the "flowers" while thinking of you*

### The overall structure

The structured part of the challenge sits between two lines of three dashes (```yaml ---```) that delimit the boundary of the YAML frontmatter, and is made of a handful of labelled sections:

```yaml
---
japanese:
  # the formatted lyrics
english:
  # English translation
song:
  # the song's title
media:
  # a link to the music
translation:
  # translation credit
---
```

Only ```yaml japanese:``` is really required; the rest just describe where the passage came from. Indentation (the spaces at the start of a line) is meaningful in YAML: it's how the format knows a word *belongs to* a line, or a reading *belongs to* a word. Keep things lined up exactly as the examples show.

Below the closing ```yaml ---``` you can optionally add body text that appears beneath the lyrics on the challenge page. It's ordinary [Markdown](https://commonmark.org/help/) (the same way you format messages in Discord!) that also supports a custom furigana syntax.[^furigana]

We'll build the ```yaml japanese:``` block first, then fill in the shorter sections after.

### Lines

Inside ```yaml japanese:```, every line of lyrics begins with ```yaml - - -``` (three dashes, with spaces between). So our two lines start out as:

```yaml
japanese:
- - - # …the words of "手鏡に映した　今は昔の面影" go here…
- - - # …the words of "君を想いながら　「花」を探す旅路" go here…
```

Most lines have just one subline, so the triple dash is almost always what is written at the beginning of a challenge line.[^layers] For a blank line between verses, use a line that reads ```yaml - - - dictionary: ~``` (or ```yaml - - - dictionary: null```). Occasionally you'll want to split one line in the original lyrics into several visual rows, that's what *sublines* are for; see [Sublines](#sublines) below.

### Words

After the ```yaml - - -```, a line is just a list of its words, each starting with a dash (```yaml -```). Every word has up to three parts:

- ```yaml pos:``` (part of speech): `noun`, `verb`, `adverb`, `adjective`, `particle`, or `phrase` (use `phrase` for "Expression", "Conjunction", or multi-word entries in Jisho).
- ```yaml text:``` the word itself, with its reading.
- ```yaml dictionary:``` *optional;*  which word on [Jisho.org](https://jisho.org) to point the word's dictionary link to. e.g. ```yaml dictionary: 私``` will point to [https://jisho.org/word/私](https://jisho.org/word/私). If you put a full URL to another website here instead, that works too.

In the snippets below, one word is shown at a time. In the real file each word is indented under its line's ```yaml - - -```.

<br>

[手]{て}[鏡]{かがみ} is a <span class="noun">noun</span>, and each kanji goes on its own line under ```yaml text```, formatted as ```yaml kanji: reading```. Every kanji *must* carry a reading:

```yaml
- pos: noun
  text:
  - 手: て
  - 鏡: かがみ
```

<br>

A run of plain hiragana with no kanji is simpler: the ```yaml text``` is written inline on one line. The particle に right after 手鏡 is just:

```yaml
- pos: particle
  text: に
```

<br>

[映]{うつ}した is a conjugated <span class="verb">verb</span>, which is where ```yaml dictionary:``` comes in. If we omit ```yaml dictionary: 映す```, then the parsed dictionary field will implicitly fall back to the text content, making the link go to [https://jisho.org/word/映した](https://jisho.org/word/映した). This page doesn't exist, only the dictionary form [https://jisho.org/word/映す](https://jisho.org/word/映す) does, so we have to specify it manually:

```yaml
- pos: verb
  text:
  - 映: うつ
  - した
  dictionary: 映す
```

<br>

Between 映した and [今]{いま} sits a full-width space. Spaces and punctuation aren't words: give them ```yaml dictionary: ~``` (or ```yaml dictionary: null```) and put the mark itself in quotes:

```yaml
- dictionary: ~
  text: "　"
```

This prevents the punctuation from linking to anything.[^english-run]

<br>

The dictionary entry isn't always exactly the dictionary form of the word as written, but you rarely have to untangle this by hand. The Wizard picks the entry for you, and the <svg class="icon"><use xlink:href="#document_check" /></svg> Validate button checks each word's reading against Jisho and flags (and in some cases auto-fills) the right link, so these oddities usually get caught for you.[^dict-irregular]

[想]{おも}い is linked to its more common spelling, [思]{おも}う, rather than 想う:

```yaml
- pos: verb
  text:
  - 想: おも
  - い
  dictionary: 思う
```

This is because only [https://jisho.org/word/思う](https://jisho.org/word/思う) exists and covers 想うunder the "Other forms" section at the bottom.

<br>

The rest of the line uses only patterns you've now seen.

### The rest of the file

With ```yaml japanese:``` done, the remaining sections are short. Each is optional except where you want it.

#### English translation

```yaml
english:
- A vestige of once upon a time, being reflected in the hand mirror
- Journey to find the "flowers" while thinking of you
```

#### Song title

```yaml
song:
  japanese: "[祭]{さい}[果]{は}ての[花]{はな}"
  english: # there is no official English title for this challenge, skip
  override: # only if you want to override the default formatting, skip
```

Use ```yaml japanese:``` for a Japanese title, with inline furigana,[^furigana] wrapped in quotes. Add an ```yaml english``` line only if there's an official English title (don't translate one yourself), or if the "Japanese title" is actually just Latin text.

```yaml override:``` is for cases like in [Tegaki Tuesday #36 — the first *One Piece* opening](/36/source) where you want to have custom formatted text *instead* of ```yaml japanese:```/```yaml english:```:

```yaml
song:
  override: the first *One Piece* opening
media: YoeP9w5UIlg
```

Gets formatted as:

> This week's challenge is from the lyrics of the first *One Piece* opening ([YouTube](https://youtube.com/watch?v=YoeP9w5UIlg))

#### Media

```yaml
media:
  type: youtube
  id: 2zXMxTFAOYE
```

```yaml type:``` is one of `youtube`, `spotify`, or `niconico`, and ```yaml id:``` is the video or track ID. There's a shorthand for YouTube if you prefer: a bare ```yaml media: 2zXMxTFAOYE``` means the same thing.

In regards to how to find the ID:

<!--
To generate a random YouTube ?si= string:
LC_ALL=C tr -dc 'A-Za-z0-9_-' < /dev/urandom | head -c 16; echo
-->

- <img src="/youtube.svg" class="icon" alt=""> **YouTube:** \
  <code>https://www.youtube.com/watch?v=<strong>dQw4w9WgXcQ</strong></code>, \
  <code>https://youtu.be/<strong>dQw4w9WgXcQ</strong>?si=IpwVnBqfg7GgZnK8</code> \
  → <code><strong>dQw4w9WgXcQ</strong></code>
- <img src="/spotify.svg" class="icon" alt=""> **Spotify:** \
  <code>https://open.spotify.com/track/<strong>4PTG3Z6ehGkBFwjybzWkR8</strong></code> \
  → <code><strong>4PTG3Z6ehGkBFwjybzWkR8</strong></code>
- <img src="/niconico.svg" class="icon" alt=""> **Niconico:** \
  <code>https://www.nicovideo.jp/watch/<strong>sm3823705</strong></code> \
  →  <code><strong>sm3823705</strong></code>

If you would like to embed some media content that's from an unsupported site, please [contact me](/about#contact).

#### Translation

```yaml
translation:
  author: Sionnach MacSionnaigh
  site:
    name: Lyrics Translate
    link: https://lyricstranslate.com/en/saihate-no-hana-flowers-sai-hate-end-festival.html
```

#### Misc.

```yaml
suggester: mochamoko
title: "[古]{ふる}[池]{いけ}や[蛙]{かわず}[飛]{と}び[込]{こ}む[水]{みず}の[音]{おと} by Bashō"
```

There are two other optional top-level fields.

- ```yaml suggester:``` the username of whoever suggested the challenge. This is mostly used for official challenges that were suggested.
- ```yaml title:``` a custom challenge name. It overrides the song-derived title everywhere the challenge is named (cards, nav, page title) and lets a song-less challenge have a name other than its slug. Supports furigana[^furigana] and Markdown syntax. (Distinct from ```yaml song.override:``` above, which only replaces the text inside the "This week's challenge is from the lyrics of …" line.)

### Sublines

A line can be split into multiple *sublines*: separate visual rows that belong to the same logical line. (There's no difference in the output between a subline and a new line; it's purely for laying lyrics out the way you want.)

To make a row a subline of the line above it, start its first word with **two** dashes (```yaml - -```) instead of three:

```yaml
japanese:
- - - dictionary: ~
      text: "line 1, subline 1, word 1"
    - dictionary: ~
      text: "line 1, subline 1, word 2"
  - - dictionary: ~
      text: "line 1, subline 2, word 1"
- - - dictionary: ~
      text: "line 2, subline 1, word 1"
```

### Putting it all together

Here's the whole thing, with the two lines we built and every section filled in:

```yaml
---
japanese:
- - - pos: noun
      text:
      - 手: て
      - 鏡: かがみ
    - pos: particle
      text: に
    - pos: verb
      text:
      - 映: うつ
      - した
      dictionary: 映す
    - dictionary: ~
      text: "　"
    - pos: noun
      text:
      - 今: いま
    - pos: particle
      text: は
    - pos: noun
      text:
      - 昔: むかし
    - pos: particle
      text: の
    - pos: noun
      text:
      - 面: おも
      - 影: かげ
- - - pos: noun
      text:
      - 君: きみ
    - pos: particle
      text: を
    - pos: verb
      text:
      - 想: おも
      - い
      dictionary: 思う
    - pos: particle
      text: ながら
      dictionary: 乍ら
    - dictionary: ~
      text: "　「"
    - pos: noun
      text:
      - 花: はな
    - dictionary: ~
      text: "」"
    - pos: particle
      text: を
    - pos: verb
      text:
      - 探: さが
      - す
    - pos: noun
      text:
      - 旅: たび
      - 路: じ
english:
- A vestige of once upon a time, being reflected in the hand mirror
- Journey to find the "flowers" while thinking of you
song:
  japanese: '[祭]{さい}[果]{は}ての[花]{はな}'
media:
  type: youtube
  id: 2zXMxTFAOYE
translation:
  author: Sionnach MacSionnaigh
  site:
    name: Lyrics Translate
    link: https://lyricstranslate.com/en/saihate-no-hana-flowers-sai-hate-end-festival.html
---
```

---

That's everything you need. If you get stuck, the Wizard is always there to give you a working starting point you can tweak by hand.

[^furigana]: The Markdown challenge body, `song.japanese` field, and ```yaml title``` field all support furigana syntax. `[漢]{かん}[字]{じ}` → [漢]{かん}[字]{じ}, `[振]{ふ}り[仮]{が}[名]{な}` →  [振]{ふ}り[仮]{が}[名]{な}. Always do per-kanji furigana besides cases like `[今日]{きょう}` → [今日]{きょう} where it's a compound reading that genuinely can't be subdivided.

[^layers]: Why three dashes? ```yaml japanese:``` is really a three-layered list (a list of lines, each line a list of *sublines*, and each subline a list of words), so ```yaml - - -``` opens all three levels at once.

[^english-run]: The same ```yaml dictionary: ~``` pattern works for runs of English text inside a challenge; see [Tegaki Tuesday #248 — きおくの[飼]{し}[育]{いく}、なみだの[培]{ばい}[養]{よう}](/248/source).

[^dict-irregular]: Jisho's slug almost always uses kanji, even for words usually written in kana (その → [其の](https://jisho.org/word/其の)). And when a kanji has several entries Jisho numbers them, not always in the order you'd expect. For example, [僕](https://jisho.org/word/僕) is しもべ ("manservant") while the everyday ぼく ("I/me") is [僕-1](https://jisho.org/word/僕-1), so the *common* reading is the suffixed one. Write the suffix straight into the field: ```yaml dictionary: 僕-1```.
