# GHEM
Gloomhaven Expanded Modding

## ABOUT
GHEM is a forever-free assembly edit of the digital edition of Gloomhaven. Its goals, in order of priority, are to:
1. expand the game's modding framework to provide additional ability types, class mechanics, conditions, and card layout options
2. fix bugs that are present in the latest released version of the game
3. eventually facilitate the use of custom art resources within the game

## SHOWCASE CLASS - RUNERIOT
The Runeriot class mod was made to showcase what GHEM can currently do. GHEM must be installed in order to use this mod.
- https://steamcommunity.com/sharedfiles/filedetails/?id=2988379237

## INSTALLATION
1. Download the GHEM folder and unzip it. You can download it by clicking the Code<> button above the list of this repository's files.
2. Locate the game's "Managed" folder, usually at C:\Program Files (x86)\Steam\steamapps\common\Gloomhaven\GH_Data\Managed OR C:\Program Files\Epic Games\Gloomhaven\GH_Data\Managed.
   - on Steam, right-click Gloomhaven in your Library and choose "Manage > Browse Local Files" from the menu. Once in the Gloomhaven folder, navigate to "GH_Data" then "Managed" in your file explorer.
   - on Epic Games, click the 3-dot icon on Gloomhaven in your Library and choose "Manage" from the menu. In the new window, click the folder icon next to the "Uninstall" button. Once in the Gloomhaven folder, navigate to "GH_Data" then "Managed" in your file explorer.
3. Copy the "GH.Runtime.dll" and "ScenarioRuleLibrary.dll" files from the GHEM folder into the game's "Managed" folder, replacing both files. Backups of the replaced files have been provided for you in case something goes wrong.

## SINGLEPLAYER WITH GHEM
GHEM is meant to have little to no impact on the base video game, meaning that current and future unmodded saves will play the same as they always have, excepting some bug fixes. The great majority of GHEM can only be accessed within the game's Modding section, which is currently only available on the Steam release of Gloomhaven.

## MULTIPLAYER WITH GHEM
As stated above, GHEM is not meant to impact the base video game. If you have GHEM installed but other players do not, you will still be able to play unmodded multiplayer without issue. MODDED MULTIPLAYER HAS NOT BEEN TESTED, but it is theoretically possible as long as each player has GHEM installed locally. If you attempt modded multiplayer, keep in mind that even without GHEM it can and will take a few minutes to load in as a non-host.

## MODDING WITH GHEM
It is recommended to browse the Runeriot's ability cards to see the correct usage of current GHEM mechanics.
- [ ] TO DO: Create documentation akin to the official documentation at https://www.flamingfowlstudios.com/GloomhavenModding/docs-page.html

## CONTRIBUTING TO GHEM
GHEM was created using dnSpy 6.3.0 (https://github.com/dnSpy/dnSpy.git). Visit the tool's repository for instructions on getting started with it.

So far, GHEM has only altered two of the game's assembled files - "GH.Runtime.dll" and "ScenarioRuleLibrary.dll" - and it is likely that your change should be in one of these files. All game mechanics (ability types, class mechanics, conditions, etc.) are defined within "ScenarioRuleLibrary.dll" and therefore any new ones should be there as well.

### TIPS
It is recommended to take some time looking around the file assembly within dnSpy and experimenting with its UI. Initially, it can be quite daunting. For questions and help, I can be reached directly on Discord with the username **acenm5**. Some of my general tips are below:
1. Some classes (sections of files) are VERY large. Ctrl + F is your friend.
2. A single new mechanic will often require additions to multiple classes that reference each other. **MAKE SURE TO NOT REFERENCE SOMETHING THAT DOESN'T EXIST IN THE SAVED FILE YET.** For a new ability, this would mean making the new ability class first, SAVING, then referencing it with the parser and anything else.
3. If unfamiliar errors are thrown when you try to save an edit, first save your changes in a Notepad document or similar place, then restart dnSpy and retry.
4. New mechanics have to be added to the YML Parser in ScenarioRuleLibrary.YML.AbilityData.ParseAbilityProperties() at very specific places, depending on the name of the mechanic. If you know how, you can translate the name to a UINT yourself and find the right place, but GHEM will also show you the UINT in the validation log when you attempt (and fail) to validate a mod that has your new ability in its ability card YMLs.
   - There are many YML parsers, and therefore no guarantee that the relevant parser has this UINT-checker implemented for you. If you add a UINT-checker to a parser, keep it in your commit so that others can benefit.
5. Gloomhaven keeps logs at C:\Users\YourUsername\AppData\LocalLow\FlamingFowlStudios\Gloomhaven, which can come in handy for debugging. "Player.log" is long and detailed, while "Simple.log" is, obviously, simpler. Write to "Simple.log" by pasting in the following header and code snippet:
```
using SharedLibrary.SimpleLog;
...
SimpleLog.AddToSimpleLog("This would be added to the log.", true);
```

#### Predicates (Func)
**Predicates are not handled well by dnSpy. They look something like this when decompiled:**
```
Func<MonsterYMLData, bool> <>9__17;
for (i = 0; i < 4; i = i4 + 1)
{
    IEnumerable<MonsterYMLData> monsters2 = ScenarioRuleClient.SRLYML.Monsters;
    Func<MonsterYMLData, bool> func5;
    if ((func5 = <>9__17) == null)
    {
    	func5 = (<>9__17 = (MonsterYMLData s) => s.ID == summonNamesList[i]);
    }
    Func<MonsterYMLData, bool> func6 = func5;
    if (monsters2.SingleOrDefault(func6) != null)
    {
    	array2[i] = true;
    }
    i4 = i;
}
```

**You need to clean this up before you can save your edits. A clean version of the code above is below:**
```
Func<MonsterYMLData, bool> predicate;
for (i = 0; i < 4; i = i4 + 1)
{
    IEnumerable<MonsterYMLData> monsters2 = ScenarioRuleClient.SRLYML.Monsters;
    Func<MonsterYMLData, bool> func5;
    func5 = (predicate = (MonsterYMLData s) => s.ID == summonNamesList[i]);
    Func<MonsterYMLData, bool> func6 = func5;
    if (monsters2.SingleOrDefault(func6) != null)
    {
        array2[i] = true;
    }
    i4 = i;
}
```

#### PrivateImplementationDetails 
**Any class with PrivateImplementationDetails cannot be saved without the following code snippet added to it:**
```
internal sealed class PrivateImplementationDetails
{
    internal static uint ComputeStringHash(string s)
    {
        uint num = new uint();
        if (s != null)
        {
            num = 0x811c9dc5;
            for (int i = 0; i < s.Length; i++)
            }
                num = (s[i] ^ num) * 0x1000193;
            }
        }
        return num;
    }
}
```
