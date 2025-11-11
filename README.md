# FNF Vslice Multikey Addon Mod

![](https://github.com/TheZoroForce240/FNF-Vslice-Multikey/blob/main/github/gameplay1.jpg)

## Main Mulitkey Addon:
- Adds 1-9K support
- Custom controls (both keyboard and gamepad)
- Almost Full Charter support
- Experimental Mobile support
	- Includes an option in the settings to switch to the currently unused lanes style as its probably easier to play with on mobile
   	- Using a keyboard/gamepad on mobile is untested and may not work

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
- Crash when exiting chart playtest ([probably related to this issue](https://github.com/FunkinCrew/Funkin/issues/4400))

## How to use

### Adding Multikey to a song (through the chart editor)

- In the chart editor, there's a new dialog window that can be opened from ```Window->Key Counts```
- You can then switch the key count for the currently loaded difficulty (and you can switch to other difficulties to change those too)
- Then you can save the key counts json file to the same folder as the chart/metadata (```data/songs/songname/```)

### Adding Multikey to a song (manually)

- Find the song you want to add it to in ```data/songs/``` (where the chart and metadata is)
- Create a file called ```keyCounts.json``` (if using another variaion, it will need to have the suffix on the end (example: ```keyCounts-erect.json```)
- The json needs to be formatted something like this:
```json
{
	"version": "1.0.0",
    "keyCounts": {
        "difficultyname": 7
    }
}
```
- You can then set the difficulty name and keycount number to what you want, for example if you want the hard difficulty to be 6K, you would add ```"hard": 6```

(You may also need to refresh loaded data with F5 after adding/changing a key count json!)

### Setting up a custom notestyle
- Each notestyle that supports multikey will need to have its own set of multikey data, you can copy from the existing ones for the base note styles
- The multikey data will be stored in ```data/multikeyData/```, with it being named the same as the notestyle
- You will also need the custom notestyle script that supports multikey, you can find these in ```scripts/notestyles/```, you'll just need to copy from any of the base ones and change the class name and id in super to your notestyle's name/id
- After you've done that you can start setting up the multikey data to match your notestyle:
  - ```noteDirections``` is used for in-game naming of note animations, each direction should be unique, the max amount of note directions does not need to match the highest key count as multiple directions can be shared
  - ```noteColorsRGB``` is used for colors on the mobile lane control scheme (for each note direction)
  - ```noteAnimations```, ```strumStaticAnimations```, ```strumPressAnimations```, ```strumConfirmAnimations```, ```strumConfirmHoldAnimations```, ```splashData``` and ```holdCoverData``` work similar to regular note styles and store the animations of everything for each note direction
  - ```keyCountData``` stores data for each keycount in order:
    - ```noteDirectionIndexes``` is used to get the animations/data for each lane, it will index the other lists to get its data
    - ```singDirectionIndexes``` is used to set which sing direction is played for that lane (0 = left, 1 = down, 2 = up, 3 = right)
    - everything else should be self explanatory and can be adjusted with a custom editor
   
- The sustain/hold note spritesheet should also match the max amount of note directions set in the multikey data
- Offsets for each key count can be fixed with a custom editor that is included the editor list on the main menu

![](https://github.com/TheZoroForce240/FNF-Vslice-Multikey/blob/main/github/debug1.jpg)

![](https://github.com/TheZoroForce240/FNF-Vslice-Multikey/blob/main/github/debug2.jpg)

### Adding higher key counts?
Just add another set of data to the end of the ```keyCountData``` for each note style multikey data json, and then edit ```defaultMultikeyControls.json``` in ```data/``` to add another row, then just use the multikey debug menu to fix notestyle offsets.

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

