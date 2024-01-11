# GHEM
Gloomhaven Expanded Modding

# 2024 and Next Steps
GHEM has been updated to work with the latest official release as of January 2024 - however, the latest official release has bugs that (1) block the resuming of modded campaigns after closing/reopening the game and (2) limit the usefulness of the MoveTrap ability type, which is fortunately unused outside of one Runeriot card.

As such, there are two things to do before adding more features to GHEM:
1. Fix the two aforementioned bugs, or find meaningful workarounds for them.
2. Develop an injector, most likely using BepInEx, to replace the current implementation of GHEM (which requires manual readdition after the files are replaced with an official update).

## ABOUT
GHEM is a forever-free assembly edit of the digital edition of Gloomhaven. Its goals, in order of priority, are to:
1. expand the game's modding framework to provide additional ability types, class mechanics, conditions, and card layout options
2. fix bugs that are present in the latest released version of the game
3. eventually facilitate the use of custom art resources within the game

## SHOWCASE CLASS - RUNERIOT
The Runeriot class mod was made to showcase what GHEM can currently do. GHEM must be installed in order to use this mod.
- https://steamcommunity.com/sharedfiles/filedetails/?id=2988379237

## INSTALLATION
1. Download the GHEM repository and unzip it. You can download it by clicking the green Code button above the list of this repository's files and selecting the "Download ZIP" option.
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
GHEM was created using dnSpyEx 6.4.1 (https://github.com/dnSpyEx/dnSpy). Visit the tool's repository for instructions on getting started with it.

So far, GHEM has only altered two of the game's assembled files - "GH.Runtime.dll" and "ScenarioRuleLibrary.dll" - and it is likely that your change should be in one of these files. All game mechanics (ability types, class mechanics, conditions, etc.) are defined within "ScenarioRuleLibrary.dll" and therefore any new ones should be there as well.

### LEARN FROM MY CHANGES
This git repo includes backups for the ScenarioRuleLibrary and GH.Runtime files. As a result, you can diff the files on your own device to see the alterations that were made (but keep in mind that some differences are just unimportant compiler-generated ones). There are two main ways to do this:
1. Download and install dotpeek (https://www.jetbrains.com/decompiler/) and use its Compare function to compare the altered and unaltered .dlls - this is the method that I use and have had great success with.
2. Use an assembly decompiler like ILSpy (https://github.com/icsharpcode/ILSpy/releases) to decompile, then Save Code to Visual Studio projects (or similar), then finally use WinMerge (https://winmerge.org/downloads/?lang=en) with recursion to show the differences - I have not attempted this method but it should work.

### TIPS
It is recommended to take some time looking around the file assembly within dnSpy and experimenting with its UI. Initially, it can be quite daunting. For questions and help, I can be reached directly on Discord with the username **acenm5**. Some of my general tips are below:
1. Some classes (sections of files) are VERY large. Ctrl + F is your friend.
2. A single new mechanic will often require additions to multiple classes that reference each other. **MAKE SURE TO NOT REFERENCE SOMETHING THAT DOESN'T EXIST IN THE SAVED FILE YET.** For a new ability, this would mean making the new ability class first, SAVING, then referencing it with the parser (see 4) and anything else.
3. If unfamiliar errors are thrown when you try to save an edit, first save your changes in a Notepad document or similar place, then restart dnSpy and retry. Reopening dnspy after you save a change can help relieve these headaches.
4. New mechanics have to be added to the YML Parser in ScenarioRuleLibrary.YML.AbilityData.ParseAbilityProperties() at very specific places, depending on the name of the mechanic. If you know how, you can translate the name to a UINT yourself and find the right place, but GHEM will also show you the UINT in the validation log when you attempt (and fail) to validate a mod that has your new ability in its ability card YMLs.
   - There are many YML parsers, and therefore no guarantee that the relevant parser has this UINT-checker implemented for you. If you add a UINT-checker to a parser, keep it in your commit so that others can benefit.
5. Gloomhaven keeps logs at C:\Users\YourUsername\AppData\LocalLow\FlamingFowlStudios\Gloomhaven, which can come in handy for debugging. "Player.log" is long and detailed, but it is now your only option since Simple.Log was removed during the Saber updates.
```
using SharedLibrary.Logger;
...
DLLDebug.Log("This would be your debug message to later be viewed in Player.log");
```

#### Predicates (Func)
**Predicates are not handled well by dnSpy, but luckily you can just delete them without issue. They look something like this when decompiled:**
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
for (i = 0; i < 4; i = i4 + 1)
{
    IEnumerable<MonsterYMLData> monsters2 = ScenarioRuleClient.SRLYML.Monsters;
    Func<MonsterYMLData, bool> func5;
    func5 = (MonsterYMLData s) => s.ID == summonNamesList[i];
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

#### System.Runtime.CompilerServices.CompilerGenerated
**You may see this code when trying to compile something in GH.Runtime.dll - if you do, just delete the code in its entirety. Many errors will magically disappear.**
```
[global::System.Runtime.CompilerServices.CompilerGenerated]
internal static float <ProcessRowCollection>g__GetAnchorYCountingFromTop|76_0(int i, ref global::CreateLayout.<>c__DisplayClass76_0)
{
	return default(float);
}
```
