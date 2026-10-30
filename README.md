# Song Playlist — database

Songs from Malla's Instagram saved collections **English songs** (123 accessible posts pulled),
**Songs playlist** (121), and **Songs** (1) — 245 posts total, pulled 2026-10-29. Collection metadata
counts were higher (138 / 195 / 1); the difference is posts the provider did not return as accessible.

**Spotify playlist:** [Instagram Songs](https://open.spotify.com/playlist/1G3dlgJC0MyCt4EOHt88Va) — 346 tracks.
The database below and the playlist were built from the same matched list and validated 1:1:
every track in the playlist has exactly one database record with its Spotify URI, and every
database record with a Spotify URI is in the playlist. 6 songs had no confident Spotify match
and are in the database only, with a Spotify search link.

**Webpage:** enable GitHub Pages (Settings → Pages → Deploy from branch `main`) and the site
loads at https://poojithmalla8.github.io/song_playlist — search, language/type filters, sorting,
and per-song links. The page reads the same `data/songs-*.json` shards as the backend.

## Counts

- Unique songs: 352
- With a direct Spotify track link: 346
- With an Apple Music track link (iTunes match): 323
- With a direct YouTube watch link: 196 (the rest use a labelled YouTube search link)
- Languages: {'English': 309, 'Hindi': 17, 'Urdu': 8, 'Hindi/Urdu': 12, 'Punjabi': 1, 'Spanish': 4, 'Japanese': 1}
- Types (from iTunes genre, see below): {'Pop': 111, 'Alternative / Indie': 57, 'Soundtrack': 11, 'Electronic / Dance': 28, 'Unknown': 29, 'Rock': 31, 'R&B / Soul': 11, 'Folk': 1, 'Hard Rock': 7, 'Indie Rock': 5, 'J-Pop': 5, 'Hip-Hop / Rap': 4, 'Singer/Songwriter': 6, 'Musicals': 1, 'K-Pop': 2, 'Metal': 2, 'Jazz': 3, 'Pop Latino': 4, 'Indie Pop': 7, 'Indian': 1, 'Country': 2, 'Roots Reggae': 1, 'Indian Pop': 6, 'Pop/Rock': 1, 'Latin': 2, 'Reggae': 1, 'Urbano latino': 2, 'Classical Crossover': 1, 'Bollywood': 4, 'Techno': 1, 'Contemporary R&B': 1, 'Adult Contemporary': 1, 'New Wave': 1, 'Electronica': 1, "Children's Music": 1}
- Posts with no verifiable song: 135 (listed at the bottom — mostly carousel threads whose
  songs live only in slide images; nothing was guessed for them)

## How songs were identified

Every song here was explicitly named with a title AND an artist in a post caption or in the
reel's stored description (which reads on-screen Spotify/Apple Music cards). Where caption and
description disagreed, the description's artist was preferred. Titles/artists were then matched
against Spotify search (exact title + artist-token overlap, karaoke/tribute/cover results
rejected) and the iTunes Search API. Four records were corrected by hand after a failed first
match (Desoleil, Dilaan De Rajya, Bass Go Boom, i was all over her). A few narrative artist
strings were garbled or truncated; Spotify's canonical artist was used where it matched
confidently, and the original extracted artist is not stored separately — check `source_posts`
to audit any record against its Instagram post.

## Language and type — estimates, labelled

- Language: detected from the title script (e.g. Devanagari → Hindi), transliterated Hindi/Urdu/
  Telugu/Tamil/Spanish word lists, and known-artist cues. `language_confidence` is `high`,
  `medium`, or `low`. "Hindi/Urdu" means the transliterated title does not disambiguate the
  register. No Telugu songs were confidently identified among the extracted (named) songs;
  most Telugu-thread posts were unverified carousels (below).
- Type: the iTunes `primaryGenreName`, mapped to a short label (`type_source: itunes_genre`).
  29 songs had no iTunes genre match and are `Unknown`. Types are genre labels, not moods.

## Link types

- `spotify_url_type: direct` = verified Spotify track page. `search` = Spotify search for the song.
- Apple Music: a track URL when iTunes matched; otherwise the webpage shows an Apple Music
  search link (the JSON record leaves `apple_music_url` empty in that case).
- `youtube_url_type: direct` = a YouTube watch URL returned by a web search for the exact
  title + artist (some are lyric videos or full-album videos; check before sharing). `search` =
  a YouTube search-results link for the song.

## Files

- `index.html` — the webpage (loads `data/songs-*.json` via fetch).
- `data/songs-01.json` … `data/songs-04.json` — the database, sharded so each file stays
  under ~100 KB. One record per unique song (deduplicated across collections); `source_posts`
  lists every Instagram post it came from.
- `data/spotify_uris.txt` — one Spotify URI per playlist track, in database order, for
  rebuilding the playlist later without re-pulling Instagram.
- Schema per record: title, artist, language, language_confidence, type, type_source, album,
  release_year, spotify_track_id, spotify_uri, spotify_url, spotify_url_type,
  in_spotify_playlist, apple_music_url, youtube_url, youtube_url_type, artwork_url,
  source_collections, source_posts, verification, notes.

## Rebuild the Spotify playlist from this repo

Create a playlist, then add every URI in `data/spotify_uris.txt` (the playlist created
2026-10-29 is `spotify:playlist:1G3dlgJC0MyCt4EOHt88Va`).

## Unverified posts (135)

These posts named no title + artist pair in caption or reel-description text, so no song
record was created for them. Most are carousel threads whose song lists live only in slide
images, or reels whose song lives only in the audio. Links are Instagram post URLs.

- https://www.instagram.com/reel/DdhBqhvoJw-/ — title 'Hey There Delilah' named but artist not stated in caption/narrative
- https://www.instagram.com/reel/DeHazAHMc5N/ — song ('Daddy Cop'/'Cop Cuties') discussed but artist not unambiguously stated and title form ambiguous
- https://www.instagram.com/reel/Dd9HwmzAKD5/ — song ('Cop Cuties') discussed but artist not stated in caption/narrative
- https://www.instagram.com/reel/Dc001uiOvgI/ — no song titles named in caption; narrative empty; only '6 Dua Lipa songs' stated
- https://www.instagram.com/reel/Dca7IRxNYhA/ — 30-song tracklist shown on screen but no titles transcribed in caption/narrative
- https://www.instagram.com/reel/DZnTj5hRCPB/ — performers named but no song titles stated in caption/narrative
- https://www.instagram.com/reel/DWWePg8ANId/ — title/artist only via hashtags, not named in caption/narrative text
- https://www.instagram.com/reel/DRVCZ7Skgf7/ — artists named but no song titles stated in caption/narrative; titles only on album covers
- https://www.instagram.com/reel/DP4RppBDxQm/ — no song title/artist pair stated; 'Changes' promo wording ambiguous, narrative names no song
- https://www.instagram.com/reel/DK68lF2tmYw/ — title/artist only via hashtags; narrative empty
- https://www.instagram.com/reel/DQmCL4_Efgo/ — title/artist only via hashtags; narrative names no song
- https://www.instagram.com/reel/DQYyDkujHEB/ — countdown of 2016 songs but no titles/artists stated in caption/narrative
- https://www.instagram.com/reel/DNpzPQVt8-5/ — no song named in caption; narrative empty
- https://www.instagram.com/reel/DP4B9eJjTff/ — no song named in caption/narrative; 'Top 10 love song' countdown with no titles stated
- https://www.instagram.com/reel/DPJxqTvjAeV/ — no song named in caption/narrative; title only hinted by hashtag #iadoreyou
- https://www.instagram.com/reel/DQMEaBKASXz/ — no song named in caption/narrative; 2016-2018 mashup, artists named in narrative but no titles
- https://www.instagram.com/reel/DQHonnTiP7G/ — no song named in caption/narrative text; title/artist only in hashtags
- https://www.instagram.com/reel/DO9DReWkl8-/ — no song named in caption/narrative
- https://www.instagram.com/reel/DLz7hdWpGEH/ — no song named in caption/narrative; Gen Z countdown names artists only
- https://www.instagram.com/reel/DKW5TIuPL-r/ — no song named in caption/narrative text; title/artist only in hashtags
- https://www.instagram.com/reel/DI0gtQdMMXD/ — no song named in caption/narrative; Dua Lipa list with no titles stated
- https://www.instagram.com/reel/DHA_PhDMUKr/ — no song named in caption/narrative; Dua Lipa most-streamed list with no titles stated
- https://www.instagram.com/reel/DIpDJ1NRUhp/ — no song named in caption/narrative
- https://www.instagram.com/reel/DInQNXWSocq/ — no song title stated in caption/narrative; lyrics quoted and hashtags only, no title+artist pairing
- https://www.instagram.com/reel/DIl6NZfotxd/ — no song named in caption/narrative; German songs compilation with no titles stated
- https://www.instagram.com/reel/DHqVRCoyVEh/ — title named but no performing artist stated in caption/narrative; only lyricist/composer credits
- https://www.instagram.com/reel/DCePoSuyJM6/ — no song named in caption/narrative; mashup lyrics only, artists only in hashtags
- https://www.instagram.com/reel/DESldA4oxJn/ — title 'We Are The World' named in narrative but no artist stated
- https://www.instagram.com/reel/DFgFzdRsF8n/ — no song named in caption/narrative; artist only in hashtags
- https://www.instagram.com/reel/DESmeYpIz20/ — no song named in caption/narrative; songs-turning-10 list with no titles stated
- https://www.instagram.com/p/C4w0MQvpg_L/ — no song named in caption/narrative
- https://www.instagram.com/reel/DeetZvdO26j/ — no song named in caption/narrative; DJ remix, songs not named
- https://www.instagram.com/reel/DYXNvbCyk1f/ — title 'Binks' Sake' named in narrative but no performing artist stated
- https://www.instagram.com/reel/DdQP4hEAimy/ — Telugu titles named in narrative but no artist stated; caption credits composer Ilaiyaraaja only
- https://www.instagram.com/reel/DZ2TDoggIJY/ — Telugu titles named in narrative with film names only, no artist stated
- https://www.instagram.com/p/DYY-ZPNiDiO/ — no song named in caption/narrative; songs only in carousel slides
- https://www.instagram.com/reel/DYhrwMXgczm/ — Telugu titles named in narrative with film names only, no artist stated; caption credits composer Ilaiyaraaja only
- https://www.instagram.com/reel/DXYruI8EwkO/ — Telugu titles named in narrative with film names only, no artist stated
- https://www.instagram.com/reel/DWMLw9_jChR/ — no song named in caption/narrative; flashmob performance, song not named
- https://www.instagram.com/reel/DU8C0w9EkU6/ — no song named in caption/narrative; Telugu film song not named
- https://www.instagram.com/reel/DS1y6vukTE_/ — no song named in caption/narrative
- https://www.instagram.com/reel/DS5uPtMknLb/ — no song named in caption/narrative; Tamil song described only as audio
- https://www.instagram.com/reel/DR9jw4nAZ-7/ — title 'Jaare Jaare' and film named in caption but no artist stated
- https://www.instagram.com/reel/DRHm-eFDGx3/ — title 'Tere Bina' named in caption but no artist stated
- https://www.instagram.com/reel/DSKdyDYktLE/ — titles 'Chogada' and 'Nijanga Nenenaa' named but no artist stated
- https://www.instagram.com/reel/DRIuZeFCgdj/ — no song named in caption/narrative; 10 Bollywood songs list with no titles stated
- https://www.instagram.com/reel/DQNwIVMCaKM/ — no song named in caption/narrative text; title/artist only hinted by hashtags and a mention
- https://www.instagram.com/reel/DQraLX3k5H0/ — song titles only in hashtags, no artist stated
- https://www.instagram.com/reel/DOI7K92kkxe/ — no song named in caption/narrative; title only hinted by hashtag #parosong
- https://www.instagram.com/reel/DQDR1y4jEMD/ — no song named in caption/narrative text; title/artist only hinted by hashtags
- https://www.instagram.com/reel/DMUiyA1OZJ6/ — Telugu title named in narrative but no artist stated
- https://www.instagram.com/reel/DNklDa9xhbZ/ — no song named in caption/narrative
- https://www.instagram.com/reel/DNryfQPWk5p/ — no song named in caption/narrative
- https://www.instagram.com/reel/DLB_UAvR0Nu/ — no song named in caption/narrative
- https://www.instagram.com/reel/DF7sYC7PuAN/ — no song named in caption/narrative; performers listed but no titles
- https://www.instagram.com/reel/DGGbhHtBPfd/ — Telugu title/lyric quoted in caption but no artist stated
- https://www.instagram.com/reel/DFp-hkkTU8I/ — no song named in caption/narrative
- https://www.instagram.com/reel/DFrW-_uTx3a/ — no song named in caption/narrative text; song/artist only hinted by hashtags
- https://www.instagram.com/reel/C84kWuQJZ7a/ — song title/lyric named in caption/narrative but no artist stated
- https://www.instagram.com/reel/C-b7xZFtOUd/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/C9emFqCs-m_/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/DAQrKYPy7yZ/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CkiMvYRrgep/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/C8eIHgHy-gf/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C8AFZDfvuTj/ — titles/artists appear only as hashtags, with no explicit title+artist pairing in caption/narrative text
- https://www.instagram.com/p/C-FqpajyQvc/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C9hNMUPJYwP/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C9kBVJSMIiR/ — titles/artists appear only as hashtags, with no explicit title+artist pairing in caption/narrative text
- https://www.instagram.com/p/C75bsOISOtn/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C9mtT6aSqu3/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C9Pxb7uS_jU/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C7tsKHhNe1J/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C6_baO_JTKC/ — titles/artists appear only as hashtags, with no explicit title+artist pairing in caption/narrative text
- https://www.instagram.com/reel/C5OL_FSyhsB/ — song title/lyric named in caption/narrative but no artist stated
- https://www.instagram.com/p/C6tBukWJvF4/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C5xQxSvJNC4/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/C5DiCueSLuR/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/C5PkDt7p7Lq/ — song title/lyric named in caption/narrative but no artist stated
- https://www.instagram.com/reel/C5dk0yHJrFn/ — song title/lyric named in caption/narrative but no artist stated
- https://www.instagram.com/p/C3XHhktJF3Y/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/C0_vzshplTc/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/C1E8-boJ8q8/ — song title/lyric named in caption/narrative but no artist stated
- https://www.instagram.com/reel/C22J7kDPmR_/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/C1zExKmpMvA/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/C2mcn3NxUxj/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C1kC82GJ99g/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C1e_P9Qsuc8/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/C1YZngrxBi6/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/C1bTLCOJznE/ — song title/lyric named in caption/narrative but no artist stated
- https://www.instagram.com/p/C1n5rckpl9K/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C1cJ710So5-/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C1PaZkJN06s/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C1XMQjlyUn0/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C0oRhd0pY-r/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/C060pg_S7W4/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CzJdhfQylLa/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CzYGCjEsbxR/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CzX3omcy10z/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CzY-kCtM71d/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CzREZ7UJXRs/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Czbdb5lJjEp/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CzfWbLgoDb3/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Ck0wCodsmFJ/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Czksq7RNbzT/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CzQ7-VnJekL/ — song title/lyric named in caption/narrative but no artist stated
- https://www.instagram.com/reel/CxzSArAxtJj/ — song title/lyric named in caption/narrative but no artist stated
- https://www.instagram.com/p/ClbPBwwpX7L/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Cqp5RhPPTDE/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Cty09_NpkVP/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Ct6chcAM3Es/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Cu9eQAUJIH1/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CvCmexNpRJo/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CvM5Qfcp0I-/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CvrEOxpJEsJ/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Cpen7iPJMZx/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Cxky2dOMSaf/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CrDgqCrJT2R/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CrA94CoJcOi/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CqdH6apvqkR/ — titles/artists appear only as hashtags, with no explicit title+artist pairing in caption/narrative text
- https://www.instagram.com/reel/Cq1ziUyovsH/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CqNkfKjJBtD/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CsJMURFJZHc/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/Cymtq37pqaO/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CyV3SwPs68P/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/CyByz-vPe0P/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CyICiDHJMyF/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Cw7sNaHMPmZ/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CxiIiQgMNrw/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Cx0R7uapQRW/ — titles/artists appear only as hashtags, with no explicit title+artist pairing in caption/narrative text
- https://www.instagram.com/p/Cw9fRQ3JeCf/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/Cx5Yj2RMpiy/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/Cv5IPUbJxnA/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/reel/CuWRBk0pCWf/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CxGIE6UpOVB/ — no song named in caption/narrative; songs only in carousel slides/audio
- https://www.instagram.com/p/CLj0xbjgROQ/ — no song named in caption/narrative; songs only in carousel slides/audio
