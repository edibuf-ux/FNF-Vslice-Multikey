# FNF Vslice Multikey Addon Mod

## Main Mulitkey Addon:
- Adds 1-9K support
- Almost Full Charter support
- Experimental Mobile support (very buggy and wip!)

## Multikey Charts:
Adds an extra multikey difficulty for certain songs:
- All Songs Week 1-6 (ported from my really old kade engine extra keys, charts have had slight tweaks in the port to fix note timings but are mostly the same)
- Bopeebo Erect (8K)
- 2Hot (7K)
- Senpai Pico (6K)

(More songs may be added in the future!)

## Known Issues:
- Notes flickering in the charter (this usually fixes itself after placing a note)
- Visual bug when stretching a sustain in the charter
- Charter hitsounds not matching the notes at higher keycounts
- Mobile issues when restarting song or changing difficulty (strumline dissappearing, input not working, crashes)

## How to use

### Adding Multikey to a song
- Find the song you want to add it to in data/songs/ (where the chart and metadata is)
- Create a file called keyCounts.json (if using another variaion, it will need to have the suffix on the end (example: keyCounts-erect.json)
- The json needs to be formatted like this:
```json
{
    "keyCounts": {
        "difficultyname": 7
    }
}
```
- You can then set the difficulty name and keycount number to what you want

(You will also need to refresh loaded data with F5 after adding/changing a key count json!)

### Setting up a custom notestyle


### Extra Characters?
