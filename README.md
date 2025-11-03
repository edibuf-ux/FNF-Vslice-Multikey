# FNF Vslice Multikey Addon Mod

![](https://github.com/TheZoroForce240/FNF-Vslice-Multikey/blob/main/github/gameplay1.jpg)

## Main Mulitkey Addon:
- Adds 1-9K support
- Almost Full Charter support
- Experimental Mobile support (very buggy and wip!)

## Multikey Charts Addon:
Adds an extra multikey difficulty for certain songs:
- All Songs Week 1-6 (ported from my really old kade engine extra keys, charts have had slight tweaks in the port to fix note timings but are mostly the same)
- Bopeebo Erect (8K)
- 2Hot (7K)
- Senpai Pico (6K)

(More songs may be added in the future!)

![](https://github.com/TheZoroForce240/FNF-Vslice-Multikey/blob/main/github/gameplay2.jpg)

## Known Issues:
- Notes flickering in the charter (this usually fixes itself after placing a note)
- Visual bug when stretching a sustain in the charter
- Charter hitsounds not matching the notes at higher keycounts
- Mobile issues when restarting song or changing difficulty (strumline dissappearing, input not working, crashes)

## How to use

### Adding Multikey to a song
- Find the song you want to add it to in ```data/songs/``` (where the chart and metadata is)
- Create a file called ```keyCounts.json``` (if using another variaion, it will need to have the suffix on the end (example: ```keyCounts-erect.json```)
- The json needs to be formatted something like this:
```json
{
    "keyCounts": {
        "difficultyname": 7
    }
}
```
- You can then set the difficulty name and keycount number to what you want, for example if you want the hard difficulty to be 6K, you would add ```"hard": 6```

(You will also need to refresh loaded data with F5 after adding/changing a key count json!)

### Setting up a custom notestyle
- Each notestyle that supports multikey will need to have its own set of multikey data, you can copy from the existing ones for the base note styles
- The multikey data will be stored in ```data/multikeyData/```, with it being named the same as the notestyle
- You will also need the custom notestyle script that supports multikey, you can find these in ```scripts/notestyles/```, you'll just need to copy from any of the base ones and change the class name and id in super to your notestyle's name/id
- After you've done that you can start setting up the multikey data to match your notestyle:
  - Strum idle/static animations are setup with ```staticPrefix``` + ```strumNames```, the index is determined by the ```noteDirectionIndexes``` for each keycount in ```keyData```
  - Strum press animations are setup with ```pressPrefix``` + ```noteDirections```
  - Strum confirm animations are setup with ```confirmPrefix``` + ```noteDirections```
  - note animations are setup with ```notePrefix``` + ```noteDirections```
  - ```noteColors``` is used for mainly used for internal animation naming, so do whatever you want
  - ```noteColorsRGB``` is used for colors on the mobile lane control scheme
  - ```splashData``` and ```holdCoverData``` works similar to regular note styles, but are indexed by the ```noteDirectionIndexes``` for each keycount in ```keyData```
  - ```keyData``` stores data for each keycount in order:
    - ```noteDirectionIndexes``` is used to get the animations/data for each lane, it will index the other lists to get its data
    - ```singDirectionIndexes``` is used to set which sing direction is played for that lane (0 = left, 1 = down, 2 = up, 3 = right)
    - everything else should be self explanatory and can be adjusted with a custom editor
   
- Offsets for each key count can be fixed with a custom editor that is included the editor list on the main menu

![](https://github.com/TheZoroForce240/FNF-Vslice-Multikey/blob/main/github/debug1.jpg)

![](https://github.com/TheZoroForce240/FNF-Vslice-Multikey/blob/main/github/debug2.jpg)

### Extra Characters Example

The mulitkey script overrides the default character anims, both bf and dad have characterType set to OTHER while multikey is active.

So doing something like extra characters needs to be done differently

```haxe
import MultikeyCharacterHandler;

class YourSong extends Song
{
    public function new() {
		super('yoursong');
	}

    var extraChar:BaseCharacter;
    function onCreate(event:ScriptEvent):Void
    {
      super.onCreate(event);
  
      extraChar = CharacterDataParser.fetchCharacter('pico');
      PlayState.instance.currentStage.add(extraChar);
      PlayState.instance.currentStage.characters.set('pico', extraChar);
    }

    override public function onSongLoaded(event) {
        super.onSongLoaded(event);
        MultikeyCharacterHandler.disableDefaultDadAnims = true; //allows you to set up your own anims
    }

    override public function onNoteHit(event) {
        super.onNoteHit(event);
        if (!MultikeyCharacterHandler.getMustHitNote(event.note)) {
          
          if (event.note.kind == "extraChar") {
            extraChar.playSingAnimation(MultikeyCharacterHandler.getNoteSingAnimationDir(event.note), false);
            extraChar.holdTimer = 0;
          } else {
            var dad = PlayState.instance.currentStage.getDad();
            dad.playSingAnimation(MultikeyCharacterHandler.getNoteSingAnimationDir(event.note), false);
            dad.holdTimer = 0;
          }
        }
    }
}
```

