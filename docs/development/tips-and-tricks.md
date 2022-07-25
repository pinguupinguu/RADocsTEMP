## Memory Digging Tips

See also: [Memory Inspector Overview](Memory-Inspector-Overview).

- Start with 8-bit view in the Memory Viewer. Looking at larger chunks of memory will complicate things, but it will make a seasoned developer's job easier. If you're just starting out and unaware of terms like **endianess** and **bit ordering** (or when the eyes are tired), its best to make your observations in 8-bit view. Its just easier!

- **Uncheck "Show Decimal Values"** in the Achievement Editor so you can enter the hex values you see in the Memory Viewer. Its better than using a programmers calculator!

- If you find an address, also search the same region surrounding it and you could find many more useful addresses.

- You can input values in the memory viewer to see if it affects the game.

- Even if something is visibly decreasing in-game, like health, memory can behave differently. It's possible for it to be increasing, so since it's uncommon for you to know for sure, prefer the `!=` and `=` filters.

- If you absolutely cannot reduce the results of a filter, you can try guessing some values or at least how the value is acting (`>` or `<`). For example, no health is usually `0`, Stage 1 is usually value `0`, Stage 2 is usually value `1` depending on the type of game.

- **16/32 bit view**: remember that when working with 16 or 32 bit address the address to the right comes first. Example: if in 8bit it looks like `AB CD WX YZ`, in 16 bit it becomes `CDAB YZWX`. This is called "[big endian byte ordering](https://en.wikipedia.org/wiki/Endianness#Big-endian)". You can safely ignore the geek-speak by just remembering things are stored **backwards** in 16 bit view and above.

- **Bit fields**: Use single bits for things that only change a single bit. In the Memory Inspector, when an address is selected, there will be `Bits: 7 6 5 4 3 2 1 0` above the addresses. And below those numbers will be either `0` or `1`, like `0 0 1 0 0 0 1 1` in the image below. If `bit5` has `1` under it, then `bit3=1` for that address. Single bit usage is very common for game items/events/unlocks/etc. This may be confusing, but it's very important to understand. Some games with limited memory space use bit fields a lot! Its an important technique to learn.

![memsizes](/development/images/memsizes.png)


## Achievement Creation Tips

See also: [Unwelcome Concepts](Developers-Code-of-Conduct#unwelcome-concepts) and [Achievement Design](Achievement-Design).

- **~~Never~~ Avoid using only one condition as much as you can**. When using only one condition the achievement will most likely pop at the wrong time.  This is the **most common error by a developer**.

- If the game has a **demo mode** (AI plays the game after you wait a while on the title screen), make sure to find the address for it and add a condition so your achievements don't trigger in demo mode. You'll probably need to know how to use [PauseIf](Achievement-Logic-Features#pauseif) and [ResetIf](Achievement-Logic-Features#resetif).

- **`PauseIf` cheat codes**: If the game has a cheat code to increase lives, select stages, etc. Make sure to find the addresses for it and add some kind of protection in your achievements.

- If you wanna create an achievement for **collecting a specific and unique item** in the game, add some conditions to make sure the player got the item where it is supposed to be obtained, [like in this template here](Achievement-Templates#collect-an-item-in-a-specific-level). Otherwise the achievement may trigger by using password, loading a save game, etc.

- **OR conditions**: if you need OR conditions, you'll need [Alt groups](Achievement-Logic-Features#alt-groups).

- **Avoid Achievement spam**. If a game has 100 levels, do not make an achievement for each level. Do every 25 or so. Consider the time segment a casual player would unlock these progressive Achievements in. If generally you could unlock all in a progressive set of Achievements of this nature in under half an hour as a casual player, that's filler design, and not recommended.

- **Be Creative and Fun**: Get as creative as possible with achievements. "Beat X without using/doing Y", "Beat X in 5 seconds", "3 heart run". Be sure casual players can still get these with their best effort. It isn't fun if its way too difficult for a casual player!  (See:  **Bonus Set Tips** below for the real challenges!). There is a whole page about [Achievement Design](Achievement-Design) that can give good advices on how to think about good achievements.

- **Glitchless, please!**: Do not make achievements requiring glitches. Glitches, in most cases, corrupt and destabilize game memory and RAM. They break the rest of the set to include. It is however, wise to code block walls stopping players from using them to get easy mastery.

- **Avoid relying on text and graphics**: Most of the time, an achievement that requires triggering a special event can be tied to an event flag (usually a single bit or byte), or to an item received by the player. It is better practice to use these values than to check the ID of text being displayed in a text box, or, even worse, video RAM (memory tied to the actual graphics displaying on the screen) as they are often less stable. Especially between different versions of a single game, and they are more difficult to debug.

- **We love Art**:  Pretty badges are rewarding badges! It should visually represent the achievement itself, or hint at the location in the game for secrets.  Matching to the game's art style works well.  Google Image searches for "Sprite Sheets" is an useful tip to include some game art. If you're a good graphics artist, updating badges is your time to shine! More info can be found at the [Badge and Icon Creation page](Badge-and-Icon-Creation).

- **Regional Consideration**: For games with text-triggered achievements (especially RPGs) it's recommend to find an event flag instead of hooking onto text or text ID. Text presentation varies between regional versions making multi-region support difficult.


## Naming Convention Tips

- **No Symbols**: Do not use accented/special characters like the `é` in Pokemon, as they currently break a few things (like feed). They are also not visible in search without the special character. Same for UTF-8 Universal Emoji codes. **Allowed exception**: [Rich Presence Scripts](Rich-Presence).

- **Mind your English**: avoid grammar mistakes. As RetroAchievements is an international community, using English for Code Notes is a good practice, as another developer can use your notes in the future.

## Tricks

### Lesser-Known Features
  - Double-Clicking bitflags while in 8-bit view
  - Right-Clicking addresses in the editor to jump to them in the Memory Inspector (also works with offsets to jump to the current address)
  - Highlighting conditions and holding CTRL while clicking to change a field in multiple conditions at once

### Workflow Tips
  - Using Google Sheets and Notepad++
  - Editing local file

### Finding the address for Demo Mode

An usual trick to start filtering addresses to find the Demo Mode is to let the demo begin and then start to filter `=` "Last Known Value" and press "Filter" many times, stopping right before the demo ends (be careful to not filter after the demo ends).

Now start the game normally and when you're able to control the character change the filter to `!=` and then filter *one single time*. The filtered addresses are the ones that has changed since the demo mode.

Now change the filtering back to `=` and then start a sequence of play-and-filter. Which means play a bit, move around then scenario, defeat some enemies, and then filter.

**Pro tip**: It is very very very useful to have savestates in different levels of the game, so you can do the play-and-filter in different scenarios and make the filtering more effective.

After getting a few addresses as result, quite often the demo value will be `1` (sometimes it's also `ff`). If you find a potential address edit it to `0` and check if you become able to control the character. If yes, BINGO!, that's the address you're looking for.


### Address Watcher

Here's an easy trick using a dummy achievement to watch an address for any changes, which is something you'll quite often want to know.

![](/development/images/address-watcher.png)

**Conditions Explained**  

1. ``Value 1 = Value 0``; This is to prevent the achievement from ever being true, as the value 0 never equals 1.
2. ``Value 1 = Value 1``; For this trick to work the achievement needs hits to reset. When 1 = 1 hits will be added to this condition.
3. ``ResetIf Mem 0x10 != Delta 0x10``; This is the address you want to watch. Any time there is a change the achievement will reset.

- Last of all make sure that ``Pause on Reset`` and ``Active`` are both checked. Now you will get a pop-up and emulation will pause each time this address changes.
