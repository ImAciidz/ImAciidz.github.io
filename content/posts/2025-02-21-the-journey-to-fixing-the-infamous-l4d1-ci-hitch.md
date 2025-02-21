---
date: '2025-02-21T19:33:51-04:00'
title: "The journey to fixing the infamous L4D1 common infected hitch"
# weight: 1
# aliases: ["/first"]
# tags: ["first"]
author: "aciidz"
# author: ["Me", "You"] # multiple authors
#showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
#description: "Desc Text."
#canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
#UseHugoToc: false
cover:
    image: "/2025-02-21-the-journey-to-fixing-the-infamous-l4d1-ci-hitch/cover.png" # image path/url
    #alt: "<alt text>" # alt text
    #caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    #hidden: true # only hide on current single page
    hiddenInList: false # control whether hidden /posts
    hiddenInSingle: true # hides on the post itself
#editPost:
#    URL: "https://github.com/<path_to_repo>/content"
#    Text: "Suggest Changes" # edit text
#    appendFilePath: true # to append file path to Edit link
---

For multiple years, Left 4 Dead 2 has been plagued with a bug where loading into a campaign that uses the L4D1 common infected populations will result in large hitches when they first come into your view on each level. The L4D1 CI populations were added by The Last Stand update in September 2020 (version 2.2.0.0).

Of course, nothing is ever as simple as it seems, and the issue actually goes much deeper than this. **Spoiler alert:** It's not actually a problem with the L4D1 common infected!

#### a lil warning:

This is the first time I've ever tackled a problem like this before, and I've since learned a bunch of stuff that would've made my experience of attempting to solve this issue a lot less...lengthy. If you are experienced in reverse engineering/game hacking, then expect to cringe at my prior cluelessness.

Also, this writeup has been a work in progress since October (very few sessions of actual writing though, I've just been very busy and exhausted in the time since). As a result, I've gotten a bit lazy with the writing/explanations near the end as I just want this to be done and out of my mind, so yeah.

Lastly, this article/blogpost has a bit of an identity crisis when it comes to the target audience. At some points, I explain things in a way that assumes a more casual/less technical audience, but at other points, I skip explaining things which that casual audience isn't likely to understand. At this point, I don't really have the energy to rectify this, but yeah.

Moral of the story: Don't let writing the article drag out over the course of nearly 4 months.

## Demonstration of the issue

{{< youtube rTnjJN0cVS8 >}}

## Background

For the most part, this issue went largely unnoticed for many years (at least in my circles, e.g. speedrunning). I am extremely sensitive to the most minor of hitches and performance issues, but I don't play the newest version of L4D2 very much as most of my hours on the game are spent on older versions for speedrunning purposes. If I do play the newest version of the game, it's mainly just for versus or casual custom campaigns. In 99% of cases, this bug only happens to the host of a local (listen) server, so those playing on dedicated servers are unaffected, and those playing on a local server (who aren't the host) won't experience it either. I'll cover the 1% case later on.

Prior to August 2023, if I was going to do runs of The Last Stand or All Campaigns Solo, I would just use version 2.2.0.3 (released October 2nd, 2020) as I have all of my speedrunning configs and whatnot set up there, and there were some extremely minor advantages in that version for All Campaigns Solo. As it turns out, this was the second to last version to not be affected by this issue, so I had no idea it was really a regular thing. I have done speedruns of the RocketDude mutation in the past, which has always been ideal to do on the newest version of the game, and I *did* notice the stutter issue there, but I just assumed it was related to RocketDude in some way or another.

In August 2023, The Last Stand Refresh patch/update released, which brought a rebalance to the finale of The Last Stand itself, and it also unpatched a skip on Hard Rain that was originally patched by the TLS update in 2020, so the newest version of the game had become the most viable version to use for The Last Stand Solo *and* All Campaigns Solo speedruns.

One day in September 2023 I got bored and decided I'd finally do some serious attempts of The Last Stand, but I quickly got very annoyed by the giant hitches. The first couple of clips in the demonstration video actually come from that session where I decided I was going to do TLS runs.

## Asking others

I decided to ask within the L4D Community Team discord if anyone knew about the issue. A couple of people were aware of the problem. It was suggested to try an addon that removes the L4D1 common infected populations in favor of just using the standard L4D2 common infected populations, and sure enough, that stopped the hitches. However, using *any* addons is not allowed for speedruns, so this wasn't really a solution. Someone else then explained that the L4D1 common infected shaders weren't very optimized, and that work was being done to optimize them, so that would be the main path forward to fixing the issue in the future.

## Realizations

Soon after, it occurred to me that these hitches didn't happen when I was doing All Campaigns Solo speedruns on version 2.2.0.3. This was great, because it meant there was a period where we had the L4D1 common infected *without* the hitches, so the actual bug was either caused by a game code change that Valve did, or an asset change; this wasn't something introduced by the initial TLS update itself. I decided from here to use [DepotDownloader](https://github.com/SteamRE/DepotDownloader) and [SteamDB](https://steamdb.info/) to download some more older versions of the game to see when exactly the issue started.

I did a rough binary search of sorts until I determined that version 2.2.0.4 (released October 21st, 2020) was the first version where the issue was present, which is two updates after the initial 2.2.0.3 update that speedrunners happen to use. The patchnotes for 2.2.0.4 say this:

> - Fixed a crash when decoding bones.
> - Fixed a mesh error when drawing the crosshair with colorblind mode active.
> - Fixed a double-add of the client shadow manager restore function.
> - Fixed mode-only addons sometimes getting removed on map load.
> - Fixed some military sniper activity names.
> - Changed crowbar and pitchfork to slash damage only.
> - "AllowFallenSurvivorItem" can be called outside of mutations. 
> - Talker and survival updates. 

This unfortunately doesn't give us any very good clues, so I ignored it for the time being.

Note: From this point forward, any time I mention 2.2.0.3, I am referring to the second 2.2.0.3 update from October 14th, 2020, rather than the initial 2.2.0.3 update from October 2nd, 2020.

I then decided to mix the 2.2.0.3 game binaries with the newest (at the time) version game files, and sure enough, there was no stutter. This meant that there was a very high chance that the problem was entirely related to a change to game code, and not related to any changes in asset files.

I also immediately realized that the actions of disconnecting to the main menu and clicking Done in the addons menu both caused a large hitch, which didn't happen prior to 2.2.0.4. This might sound very minor, but this actually has insane relevance and correlation to the actual problem (and is also alluded to by the patchnotes).

{{< youtube Ce4VxyalRUc >}}

However, I lacked the interest and skills required to take this any further at the time, so this issue remained dead in the water for a little over a year.

## April 2024

I was asked to test a version of the L4D1 common infected shader that was a bit more optimized. I didn't do any scientific testing or comparisons, but at best it felt like a very slight improvement, but a slight improvement on 'really bad' is unfortunately still 'really bad'. Around this time, someone else stated that it was likely being caused by the L4D1 common infected shader not being properly precached. I didn't look into the problem any further at this time, as I was generally preoccupied with other stuff, but I did keep that piece of information in mind.

## Figuring out why the lag happens

This brings us to October 2024.

I'd recently engaged in some reverse engineering/programming activities with the help of bill_play3 regarding another longstanding bug in L4D2 that has yet to be fixed (which we haven't yet made a proper fix for, but it's low priority and we know *how* to fix it. It's a story for another day), so I was a bit motivated to try and tackle other problems, such as the subject of this post.

### BinDiff

I've heard about [BinDiff](https://github.com/google/bindiff) in the past, but never had a reason to look into it or try it out. I was reading in a Discord server and saw someone mention that there were tools ([BinExport](https://github.com/google/binexport) and [BinDiffHelper](https://github.com/ubfx/BinDiffHelper)) to integrate BinDiff in Ghidra, so I decided I'd try it out. Unfortunately, my first day or or two of attempting to use BinDiffHelper was not very successful, as the plugin seems to be rather buggy on Windows (it would always hang infinitely when it invoked BinDiff on the exported binaries, and the feature to quickly diff the disassembly on a given function always broke after the first use of it in the session). Eventually, I figured out that I could manually export the desired binaries to compare from Ghidra, and then manually run BinDiff on those exports, and *then* use BinDiffHelper to import the relevant `.bindiff` file, so I was back to making some progress.

### Debug info and platform differences

The Linux dedicated server binaries for L4D2 (the files suffixed with `_srv.so` instead of just `.so`) come with some debug info, as do all of the Mac binaries. Windows binaries don't come with debug info for obvious reasons, so in order to figure out function names on Windows, you have to look for things within functions that you can use to uniquely identify them. For example, functions that reference strings are usually very easy to match between platforms. If you're looking at functions called by a known function, you can look for general similarity of the disassembly on the called function between Windows and the other platforms to be sure you're looking at the same thing. If the function you're looking for is virtual, you can find a different function that is easy to figure out the name of in the same vtable, then count the number of functions between that one and the one you want to find, then apply that math on Windows (though this is not always a 100% foolproof strategy due to code differences between the platforms and Other Things). There are of course more ways than just this, but yeah. It's not always easy, but it's far from impossible.

The Mac binaries tend to have a lot of stuff inlined, so it can sometimes be hard to find some functions by name (since they just become a part of other functions directly). For this reason, it's generally preferable to look at the Linux binaries first, and then reference the Mac binaries when you need to.

Additionally, the Linux dedicated server binaries are just that: dedicated server binaries. This means you aren't going to be able to find any functions that aren't needed for dedicated server usage, or functions might be implemented differently to account for this. Client (and other) binaries don't have a dedicated server counterpart for what should be obvious reasons. In the cases where these restrictions are problematic, you have to rely on the Mac binaries instead.

Unfortunately, for reasons unbeknownst to me, when I ran BinDiff on Mac binaries, it listed a significant amount of functions having changes, and/or it was blatantly mismatching functions (I could tell this was happening because the given function names were obviously not matching). However, this was not an issue for the Linux DS binaries - the diffs for those generally looked very similar, if not the same as the diff for the Windows binaries.

For this reason, I also occasionally referenced the 2014 build of Portal 2 that mistakenly shipped with PDBs, which was helpful for reversing and labeling functions in the smaller binaries that don't tend to change a whole lot between engine branches/games (such as `datacache`).

I learned a few months later that there's some [really handy scripts](https://github.com/Scags/IDA-Scripts) for IDA that automatically match functions between binaries based on their usage of strings, as well as being able to match entire vtables across binaries, so that's really neat.

Also, small note - the Linux dedicated server depot includes `_srv` counterparts for matchmaking, server, as well as the `matchmaking_ds`/`matchmaking_ds_srv` binaries themselves, in addition to the `_srv` binaries already available in the main L4D2 Linux client depot.

### Initial discoveries

Now that I got all of that explaining out of the way, I can get into what actually changed between 2.2.0.3(v2) and 2.2.0.4.

The `engine`, `gameui`, `materialsystem`, `serverplugin_empty`, `matchmaking_ds`, `matchmaking`, `server` and `client` binaries all changed. 

In an attempt to narrow things down, I was able to swap in just the newer `engine`, `matchmaking_ds`, `matchmaking`, `server`, and `client` binaries to the rest of the 2.2.0.3 game files, while avoiding any crashes due to obvious binary incompatibilty, and continued to experience the bug, so any problematic changes were almost certainly contained within those binaries specifically.

I first checked `engine`, which had a total of 6 functions that differed:

* `CVEngineServer::ManageAddonsForActiveSession` (will be abbreviated as MAFAS from now on)
* `CEngineClient::ManageAddonsForActiveSession` (same)
* `CollectAddonsOfType`
* `FileSystem_ManageAddonsForActiveSession` (will be abbreviated as `FSMAFAS` from now on)
* `CDownloadListGenerator::Disconnect`
* `FileSystem_UpdateAddonSearchPaths`

(obviously I did not know the function names at first, I had to label stuff before looking into any of it)

* `CVEngineServer::MAFAS` and `CEngineClient::MAFAS` are virtual functions that both call `FSMAFAS` and do nothing else.
* I don't remember what changed in `CollectAddonsOfType`, but since I know now that it was irrelevant to the problem, I'm not going to bother figuring out/describing what changed here.
* `CDownloadListGenerator::Disconnect` runs when you disconnect from a server (or just run the `disconnect` command in general), and one of the things it does is call `FSMAFAS`.
* `FileSystem_UpdateAddonSearchPaths` does a bunch of stuff, including calling `FSMAFAS`.
* `FSMAFAS` itself gained an fourth parameter (a `bool`), and its logic changed.

Given that `FSMAFAS` gained an extra parameter, some of changes in the other functions basically amount to the addition of that extra parameter in the function call.

I tried putting a breakpoint on these functions to see if any were called at the moment of the lagspike, but none of them were. However, some of these functions (namely `CollectAddonsOfType` and `FSMAFAS`) were called during the loading screen, so I began to suspect that one of these functions were doing something bad during the loading screen which ended up causing the hitches during gameplay.

### Profiling

From here, bill_play3 suggested to me that I use a profiler to try and see if there was a function that ran longer than expected when the stutter happened.

For this, I first tried using [VerySleepy](https://github.com/VerySleepy), there probably was/is a better tool for the job, but this literally was my first time trying to profile something, so yeah. I only knew about VerySleepy from hanging around the development channels of the (now defunct) Black Ops 3 modded client [BOIII](https://web.archive.org/web/20230429014631/https://github.com/momo5502/boiii).

Due to a [bug](https://github.com/VerySleepy/verysleepy/issues/109) (or design flaw, I guess) in VerySleepy, I was a bit thrown off when looking at the output for the first time, since multiple functions in the callstack were labeled as the first exported function in that module, rather than the address of the actual function in question. Because of that, I decided to just try something else before digging into this further.

I remembered previously reading a [google doc](https://docs.google.com/document/d/1v8QtbUtGAf6OapE9iWn4Y4STA7Scn_hr1Yih1nf2_BA) written by Valve that explained how to send them information to help debug CS2 performance issues, so I decided to find that doc again and see what software was used for that so I could attempt to use it here.

That brought me to [perfview](https://github.com/microsoft/perfview). While I didn't really know how to use the program properly, I was able to extract some very useful information from it.

Within perfview I found a callstack that was seemed identical to what I'd already seen in VerySleepy. I also got to see that the game was spamming file open calls in this call stack, hence the lag. ![image](/2025-02-21-the-journey-to-fixing-the-infamous-l4d1-ci-hitch/perfview.png) Since the L4D1 common infected are comprised of so many materials, the game was doing a substantial amount of raw FS searching to locate these. I also realized shortly afterward that the issue was present on every campaign - it was just much less noticeable because, for example, the L4D2 CI population consists of very few materials, so a campaign using those (like Dark Carnival) had much fewer (and therefore smaller) hitches to begin with.

I don't know how Source Engine typically searches for files that are contained in a VPK, but at the time, I thought this behavior of spamming raw FS calls in every possible directory where these materials could be located, despite these materials being contained within the VPKs could potentially have implications with regards to what's really wrong.

From here, I decided to figure out exactly what functions were contained in these callstacks.

{{< detail-tag "Callstack 1 (click to expand)" >}}

| Function the offset is inside of (next function on the list is what calls this)                                 | Module           | Offset (points to CALL instruction) |
| --------------------------------------------------------------------------------------------------------------- | ---------------- | ----------------------------------- |
| CreateFileA (this calls CreateFileW)                                                                            | KERNELBASE       | CreateFileA+2C                      |
| CWin32ReadOnlyFile::FS_fopen                                                                                    | filesystem_stdio | 1727B                               |
| CFileSystem_Stdio::FS_fopen                                                                                     | filesystem_stdio | 173A7                               |
| CBaseFileSystem::Trace_FOpen                                                                                    | filesystem_stdio | A0A9                                |
| CBaseFileSystem::HandleOpenRegularFile                                                                          | filesystem_stdio | AEF2                                |
| CBaseFileSystem::FindFile                                                                                       | filesystem_stdio | B273                                |
| CBaseFileSystem::FindFileInSearchPaths                                                                          | filesystem_stdio | CF5B                                |
| CBaseFileSystem::OpenEx                                                                                         | filesystem_stdio | D3A1                                |
| CBaseFileSystem::Open                                                                                           | filesystem_stdio | 1A1F                                |
| KeyValues::LoadFromFile                                                                                         | materialsystem   | 76E33                               |
| LoadVMTFile                                                                                                     | materialsystem   | 6F1B                                |
| CMaterialSystem::FindMaterial                                                                                   | materialsystem   | 19015                               |
| CStudioRenderContext::LoadMaterials                                                                             | studiorender     | 657C5                               |
| CStudioRenderContext::LoadModel                                                                                 | studiorender     | 69E03                               |
| CMDLCache::BuildHardwareData                                                                                    | datacache        | 63EA                                |
| CMDLCache::ProcessDataIntoCache                                                                                 | datacache        | F8C0                                |
| CMDLCache::ProcessPendingAsync                                                                                  | datacache        | DF32                                |
| CMDLCache::UnserializeHardwareData                                                                              | datacache        | F2A1                                |
| CMDLCache::GetHardwareData                                                                                      | datacache        | F379                                |
| CModelRenderSystem::AddModelToLists                                                                             | client           | 13F40F                              |
| CModelRenderSystem::DrawModels                                                                                  | client           | 141A82                              |
| CRendering3dView::DrawOpaqueRenderables                                                                         | client           | 1CF3E6                              |
| CBaseWorldView::DrawExecute                                                                                     | client           | 1D0202                              |
| CSimpleWorldView::Draw                                                                                          | client           | 1D1A96                              |
| CSimpleRenderExecutor::AddView                                                                                  | client           | 1C694E                              |
| CViewRender::DrawWorldAndEntities                                                                               | client           | 1CDD20                              |
| CViewRender::ViewDrawScene                                                                                      | client           | 1CE974                              |
| CViewRender::RenderView                                                                                         | client           | 1D3EA4                              |
| CViewRender::Render                                                                                             | client           | 1B89FE                              |
| CHLClient::View_Render                                                                                          | client           | A6134                               |
| V_RenderView                                                                                                    | engine           | 11626C                              |
| SCR_UpdateScreen                                                                                                | engine           | D38EA                               |
| \_Host\_RunFrame\_Render                                                                                        | engine           | 18E5B3                              |
| \_Host\_RunFrame                                                                                                | engine           | 191049                              |
| Host_RunFrame                                                                                                   | engine           | 1912FE                              |
| CHostState::State_Run                                                                                           | engine           | 1A09A6                              |
| CHostState::FrameUpdate                                                                                         | engine           | 1A0AFA                              |
| HostState_Frame                                                                                                 | engine           | 1A0B9F                              |
| CEngine::Frame                                                                                                  | engine           | 200CBF                              |
| CEngineAPI::MainLoop                                                                                            | engine           | 1FF073                              |
| CModAppSystemGroup::Main (?)                                                                                    | engine           | 1FF19A                              |
| (seems to be lack of continuity here, does not call func listed above this in table, regardless, not important) | engine           | 20079F                              |
| Unknown (doesn't matter)                                                                                        | launcher         | LauncherMain+393C                   |
| Unknown (doesn't matter)                                                                                        | left4dead2       | 1237                                |
| Unknown (doesn't matter)                                                                                        | left4dead2       | 17E9                                |

{{< /detail-tag >}}

{{< detail-tag "Callstack 2 (click to expand) - differences to the first are bolded" >}}

| Function the offset is inside of (next function on the list is what calls this)                                 | Module             | Offset (points to CALL instruction) |
| --------------------------------------------------------------------------------------------------------------- | ------------------ | ----------------------------------- |
| CreateFileA (this calls CreateFileW)                                                                            | KERNELBASE         | CreateFileA+2C                      |
| CWin32ReadOnlyFile::FS_fopen                                                                                    | filesystem_stdio   | 1727B                               |
| CFileSystem_Stdio::FS_fopen                                                                                     | filesystem_stdio   | 173A7                               |
| CBaseFileSystem::Trace_FOpen                                                                                    | filesystem_stdio   | A0A9                                |
| CBaseFileSystem::HandleOpenRegularFile                                                                          | filesystem_stdio   | AEF2                                |
| CBaseFileSystem::FindFile                                                                                       | filesystem_stdio   | B273                                |
| CBaseFileSystem::FindFileInSearchPaths                                                                          | filesystem_stdio   | CF5B                                |
| CBaseFileSystem::OpenEx                                                                                         | filesystem_stdio   | D3A1                                |
| CBaseFileSystem::Open                                                                                           | filesystem_stdio   | 1A1F                                |
| KeyValues::LoadFromFile                                                                                         | materialsystem     | 76E33                               |
| LoadVMTFile                                                                                                     | materialsystem     | 6F1B                                |
| CMaterialSystem::FindMaterial                                                                                   | materialsystem     | 19015                               |
| **CStudioRenderContext::GetMaterialList**                                                                       | ***studiorender*** | ***66315***                         |
| **Mod_GetModelMaterials**                                                                                       | ***engine***       | ***E64C9***                         |
| **CMDLCacheNotify::ComputeModelFlags**                                                                          | ***engine***       | ***E9344***                         |
| **CMDLCacheNotify::OnDataLoaded**                                                                               | ***engine***       | ***E9482***                         |
| **CMDLCache::BuildHardwareData**                                                                                | ***datacache***    | ***642B***                          |
| CMDLCache::ProcessDataIntoCache                                                                                 | datacache          | F8C0                                |
| CMDLCache::ProcessPendingAsync                                                                                  | datacache          | DF32                                |
| CMDLCache::UnserializeHardwareData                                                                              | datacache          | F2A1                                |
| CMDLCache::GetHardwareData                                                                                      | datacache          | F379                                |
| CModelRenderSystem::AddModelToLists                                                                             | client             | 13F40F                              |
| CModelRenderSystem::DrawModels                                                                                  | client             | 141A82                              |
| CRendering3dView::DrawOpaqueRenderables                                                                         | client             | 1CF3E6                              |
| CBaseWorldView::DrawExecute                                                                                     | client             | 1D0202                              |
| CSimpleWorldView::Draw                                                                                          | client             | 1D1A96                              |
| Unknown                                                                                                         | client             | 1C694E                              |
| CViewRender::DrawWorldAndEntities                                                                               | client             | 1CDD20                              |
| CViewRender::ViewDrawScene                                                                                      | client             | 1CE974                              |
| CViewRender::RenderView                                                                                         | client             | 1D3EA4                              |
| CViewRender::Render                                                                                             | client             | 1B89FE                              |
| CHLClient::View_Render                                                                                          | client             | A6134                               |
| V_RenderView                                                                                                    | engine             | 11626C                              |
| SCR_UpdateScreen                                                                                                | engine             | D38EA                               |
| \_Host\_RunFrame\_Render                                                                                        | engine             | 18E5B3                              |
| \_Host\_RunFrame                                                                                                | engine             | 191049                              |
| Host_RunFrame                                                                                                   | engine             | 1912FE                              |
| CHostState::State_Run                                                                                           | engine             | 1A09A6                              |
| CHostState::FrameUpdate                                                                                         | engine             | 1A0AFA                              |
| HostState_Frame                                                                                                 | engine             | 1A0B9F                              |
| CEngine::Frame                                                                                                  | engine             | 200CBF                              |
| CEngineAPI::MainLoop                                                                                            | engine             | 1FF073                              |
| CModAppSystemGroup::Main (?)                                                                                    | engine             | 1FF19A                              |
| (seems to be lack of continuity here, does not call func listed above this in table, regardless, not important) | engine             | 20079F                              |
| Unknown (doesn't matter)                                                                                        | launcher           | LauncherMain+393C                   |
| Unknown (doesn't matter)                                                                                        | left4dead2         | 1237                                |
| Unknown (doesn't matter)                                                                                        | left4dead2         | 17E9                                |

{{< /detail-tag >}}

*​*

And it turns out, nothing interesting was called, it was just the main loop from the highest (or lowest) level of the engine/process, all the way down to attempting to search for a material and then loading it. 

I ended up looking for some hidden cvars that would log when files were opened. I wanted see the differences in what was printed for 2.2.0.3 and 2.2.0.4 when I loaded up a map (with director disabled) and then enabling the director after the load (so that I'd have precise control over when the hitch would happen). I found `fs_report_sync_opens` which was sufficient. In 2.2.0.3, a certain vmt appeared 14 times prior to me pressing my `director_start` bind, and twice after I pressed my `disconnect` bind. In 2.2.0.4, this vmt appeared 16 times before I ran `director_start`, 10 times when I pressed my `director_start` bind, and twice when I disconnected. The number of lines in the log file was also basically doubled for 2.2.0.4 compared to the 2.2.0.3 log, which was peculiar. This didn't tell me a *whole* lot, other than confirming that the game is definitely doing a lot more file open calls than it was prior to 2.2.0.4.

At this point I was still convinced that something was kicking the materials out of the cache during the load, and the game was committing war crimes to late-load them. Since I was basically at a dead end again, I looked at the callstack for the `CreateFileA` calls. Bill suggested I repeat my testing that I described above with `fs_report_sync_opens` as a client of a server (rather than as the host of a local server), and the results I got as a client (on 2.2.0.4) were identical to what I previously got on 2.2.0.3 (as the host), but this didn't really tell me much.

Also, at some point during my testing around `fs_report_sync_opens`, **I realized that this extended to *all* materials, not just the CI population.** So that's even worse.

### 🦀 FSMAFAS is evil 🦀

Shortly after this, I was messing around and I just happened to notice that on 2.2.0.4, with `developer` enabled, `Trying cache : '<path>'` would appear for every sound cache the game normally uses twice during the load, which did not happen on 2.2.0.3. ![image](/2025-02-21-the-journey-to-fixing-the-infamous-l4d1-ci-hitch/sndcache1.png) ![image](/2025-02-21-the-journey-to-fixing-the-infamous-l4d1-ci-hitch/sndcache2.png) I searched in ghidra for the string, and found it within `CAudioSourceCache::LoadMasterCache`. I then put a breakpoint on that function in WinDbg and generated a callstack when it was hit. I quickly realized that `FSMAFAS` was contained in the callstack, **which was one of the functions that changed in the update** (to be more precise, `FSMAFAS` was calling `CAudioSourceCache::ReloadSoundCaches`, which then calls `CAudioSourceCache::LoadMasterCache`). This was a pretty profound discovery. However, the callstacks differed between the first and second times this was called in the load - the first time, a function in `server` was involved, the second time, a function in `client` was involved. I decided to label the callstacks:

{{< detail-tag "Callstack 1 (first call in the load - click to expand)" >}}

| function that contains the call instruction the offset leads to                                                     | offset        |
| ------------------------------------------------------------------------------------------------------------------- | ------------- |
| this is CAudioSource::LoadSoundCaches itself aka where I set the bp                                                 | engine+415B0  |
| FileSystem_ManageAddonsForActiveSession (offset calls thunk function that calls something else and LoadSoundCaches) | engine+17BA1D |
| CVEngineServer::ManageAddonsForActiveSession                                                                        | engine+131408 |
| CTerrorAddonsEclipseContent::FireGameEvent                                                                          | server+2D6450 |
| CGameEventManager::FireEventIntern                                                                                  | engine+17F705 |
| CGameEventManager::FireEvent                                                                                        | engine+180916 |
| CGameServer::SpawnServer                                                                                            | engine+1242E5 |
| Host_NewGame                                                                                                        | engine+18ED7B |
| CHostState::State_NewGame                                                                                           | engine+1A042A |
| CHostState::FrameUpdate                                                                                             | engine+1A0A76 |
| HostState_Frame                                                                                                     | engine+1A0B9F |
| CEngine::Frame                                                                                                      | engine+200CBF |
| CEngineAPI::MainLoop                                                                                                | engine+1FF073 |
only went to `CEngineAPI::MainLoop` because it's just the same shit and not relevant

{{< /detail-tag >}}

{{< detail-tag "Callstack 2 (second call in the load - click to expand)" >}}

| function that contains the call instruction the offset leads to                                                     | offset        |
| ------------------------------------------------------------------------------------------------------------------- | ------------- |
| this is CAudioSource::LoadSoundCaches itself aka where I set the bp                                                 | engine+415B0  |
| FileSystem_ManageAddonsForActiveSession (offset calls thunk function that calls something else and LoadSoundCaches) | engine+17BA1D |
| CEngineClient::ManageAddonsForActiveSession                                                                         | engine+5CF83  |
| CTerrorAddonsEclipseContent::FireGameEvent                                                                          | client+2782A9 |
| CGameEventManager::FireEventIntern                                                                                  | engine+17F705 |
| something that calls fireeventintern                                                                                | engine+18093B |
| CBaseClientState::ProcessServerInfo                                                                                 | engine+55C91  |
| CClientState::ProcessServerInfo                                                                                     | engine+1EAD82 |
| some undefined function (might be _ProcessMessages idk)                                                             | engine+4F19D  |
| CNetChan::CheckReceivingList                                                                                        | engine+1BC2E5 |
| CNetChan::ProcessPacket                                                                                             | engine+1BC983 |
| NET_ProcessSocket                                                                                                   | engine+1CD6FB |
| CL_ReadPackets                                                                                                      | engine+7A47B  |
| \_Host\_RunFrame\_Client                                                                                            | engine+18E35A |
| \_Host\_RunFrame                                                                                                    | engine+190BB8 |
| Host_RunFrame                                                                                                       | engine+1912FE |
| State_Run                                                                                                           | engine+1A09A6 |
| CHostState::FrameUpdate                                                                                             | engine+1A0AFA |
| HostState_Frame                                                                                                     | engine+1A0B9F |
| CEngine::Frame                                                                                                      | engine+200CBF |
| CEngineAPI::MainLoop                                                                                                | engine+1FF073 |
excluding the last two for obvious reasons

{{< /detail-tag >}}

*​*

I checked 2.2.0.3, and while `LoadMasterCache` obviously wasn't called at all during the load, `FSMAFAS` was (twice, same as 2.2.0.4). At this point it was getting late, so I sent bill the disassembly from ghidra for both versions of `FSMAFAS` and went to bed.

Bill quickly pointed out that this change at the beginning of the function seemed suspicious. ![image](/2025-02-21-the-journey-to-fixing-the-infamous-l4d1-ci-hitch/suschange.png) After some back and forth, we decided that we wanted to see if anything wrote to the global in the `if` statement. However, ghidra didn't have any information to offer, so bill told me to set a data write breakpoint on it in Cheat Engine. Before I did that, I decided to see what would happen if I let the rest of the function execute on 2.2.0.3 (since we knew that wasn't happening, since `LoadSoundCaches` was never hit). First I stupidly tried changing the value of the global referenced in the `if` statement, but that just crashed my game (in retrospect, this causing a crash makes sense). So then I just `NOP`'d the `CMP` instruction so that it never early returned, and sure enough, the exact same stutters now existed on 2.2.0.3.

After this, I quickly realized that enabling/disabling addons modified this global. Enabling Daroot Leafstorm's [Fortnight Challenges](https://steamcommunity.com/sharedfiles/filedetails/?id=923919052) set it to 0xB. Enabling Rayman1103's [Admin System](https://steamcommunity.com/sharedfiles/filedetails/?id=214630948) alongside Fortnight Challenges made it 0xC. I then got the data write breakpoint working, and concluded that `FileSystem_UpdateAddonSearchPaths` sets it to 0 when nothing is enabled, and `CUtlVector<>::InsertBefore` was writing to it...which explained a lot: ![alt text](/2025-02-21-the-journey-to-fixing-the-infamous-l4d1-ci-hitch/insertbefore.png)

So, this has a few hilarious implications:
* This stutter bug always (more or less) existed before 2.2.0.4, but you only ran into it if you had an addon enabled.
* This "early return if no addons are enabled" logic used in pre-2.2.0.4 caused even more problems, which I'll get to in a bit.

Valve moment: 
![image](/2025-02-21-the-journey-to-fixing-the-infamous-l4d1-ci-hitch/alwaysreload.png)

Anyways, I started studying `FSMAFAS` and labeling the virtual function calls (of which there were many). I determined the offending call to be `CModelLoader::Studio_ReloadModels`.

So, remember in the beginning when I mentioned that in 99% of cases, you won't experience the late-cache stutters as the client of a server (meaning, you joined a dedicated server or someone else's local server)? Well, here's the 1% case. If you join a server, leave, and then rejoin while the server is still on the same map, you will experience the stutters (but you are fine on subsequent level transitions in that server). If the server changes to a new map after you left (but before you rejoin), then you are also fine. `FSMAFAS` (and by extension `Studio_ReloadModels`) is being called every time you load into a game in 2.2.0.4, so there's almost certainly something else that's weird going on here - some kind of stale state regarding the current/previous map, perhaps. We never investigated this angle further (as there simply was no reason to), but knowing about this behavior is helpful if someone wants to 100% properly fix the underlying issue in the future.

## First attempted fix

bill went through and wrote an overview of what exactly `FSMAFAS` is doing: ![image](/2025-02-21-the-journey-to-fixing-the-infamous-l4d1-ci-hitch/billexplainsfsmafas1.png) ![image](/2025-02-21-the-journey-to-fixing-the-infamous-l4d1-ci-hitch/billexplainsfsmafas2.png)

From here, he suggested that maybe calling `CMaterialSytem::CacheUsedMaterials` near the end of the function would potentially fix the issue.

However, to cut to the chase, this did not work (game crash). Even if it didn't crash, it almost certainly wouldn't have produced a different outcome, because it turns out that `CacheUsedMaterials` gets called by the game four times after `FSMAFAS` (and by extension, `Studio_ReloadModels`) gets called.

## The actual fix

From here, we had two choices: Either go a level deeper to list the model and material lists at various points in the process, or try and see if omitting one of the two calls to `FSMAFAS` (when you are host) would do anything. If it did, then we could explore adding an early return condition with a function hook. We decided to go down the latter path, at it was far less complicated to explore.

First, I wanted to find out what the first `bool` parameter actually represented in `FSMAFAS`, and quickly determined that it is `true` when loading into a server that is on a gamemode which has addons disabled via `addonconfig.cfg` (like versus), and `false` if the gamemode allows addons. (so, `FSMAFAS` is definitely also responsible for disabling addons when playing on a gamemode that disallows them)

So, in total, the `FSMAFAS` parameters represent the following:
```C
bool IsAddonsDisallowed // are addons banned in the current gamemode by 
                        // addonconfig.cfg (by default: versus,
                        // versus survival, and scavenge)
char *mission // the current campaign's mission filename 
              // ex: Dark Carnival would be campaign2
char *gamemode // current gamemode (coop, versus, survival, etc)
bool IsModeAMutation // is the current mode a mutation
```

To recap, `FSMAFAS` is called by four things:
* `CDownloadListGenerator::Disconnect` - called whenever you disconnect from a server (or when `disconnect` itself is ran). The bool parameters will be false, and both `mission` and `gamemode` will be null.
* `CVEngineServer::ManageAddonsForActiveSession` - called early in the load as the host of a local server, and all parameters vary based on the conditions you would expect them to.
* `CEngineClient:ManageAddonsForActiveSession` - called late in the load whether you are the host of a local server, or the client of any server, and all parameters vary based on the conditions you would expect them to.
* `FileSystem_UpdateAddonSearchPaths` - called when you run the `update_addon_paths` command or when you exit the actual Addons menu on the main menu. This means that when `FSMAFAS` is called by this, both `mission` and `gamemode` will always be null, and both bool parameters will always be false.

If any of the parameters change (relative to the last time `FSMAFAS` was called), then we want `FSMAFAS` to execute in its entirety. However, if the `mission` and `gamemode` parameters aren't null and haven't changed since the previous call to `FSMAFAS`, then we are almost certainly loading into a new level while on the same server (whether we are the host of that server or just a client). Given that the goal of `FSMAFAS` is to adjust what addon VPKs are enabled/disabled on a per campaign/gamemode/mutation etc basis, if the campaign/gamemode haven't changed since the previous execution of `FSMAFAS` (and aren't null), then there are absolutely no VPKs that need to be loaded or unloaded. So, in that case, we can just skip calling `FSMAFAS` with our hook, which avoids the problem entirely. Additionally, the `IsAddonsDisallowed` and `IsModeAMutation` parameters should only ever change in tandem with the `gamemode` parameter, so we can just ignore the state of those entirely in our hook logic. 

Note: The mission parameter can be an empty string if you are playing a gamemode that is not supported on the current level/map (for example, Survival on No Mercy Apartments). So, if any addons are enabled and the mission parameter is an empty string, then we will allow `FSMAFAS` to execute, since we have no (easy) way to know if the campaign changed since the last time our hook ran.

With this in mind, we could now proceed to write some logic in that would control whether `FSMAFAS` itself actually executed, based off the passed parameters passed into `FSMAFAS`.

I don't feel like digging up what the first iterations of that looked like, but the tl;dr is that it worked; there were no more stutters, and addon VPKs were still unloaded and loaded as they were intended to by Valve. (Yay!!!)

### But wait, there's more!

*However*, there was still room for improvement: As explained at some point previously, prior to 2.2.0.4, `FSMAFAS` would early return (ie skip executing basically all of the logic) if no addons were enabled (because if no addons are enabled, then there is no need to go through the process of selectively enabling/disabling addon VPKs based on the gamemode or mutation). Fortunately, it was trivial to grab the vector member (the one that I tried modifying in cheat engine previously) that could be used to determine if any addons were enabled via the `show_addon_metadata` command callback, so we could use that to determine what to do fairly easily. BUT, while discussing the idea of doing this in the first place with bill, we both realized that Valve's old 'early return' logic from pre-2.2.0.4 versions of the game had a rather large issue, which I alluded to in the "`FSMAFAS` is evil" section. 

So, in versions prior to 2.2.0.4: let's say you have one addon enabled - a common infected retexture. You then decide to disable this addon. When you click the `Done` button in the addons menu after unchecking the box for that addon, `FileSystem_UpdateAddonSearchPaths` is called, which then calls `FSMAFAS`. However, since no addons are enabled at the moment which you clicked `Done`, that vector member which the older version of `FSMAFAS` used to determine if it should early return or not is already 0 - so `FSMAFAS` early returns. If you then load up a campaign, **all of your common infected will be invisible**, and the console will get hammered with errors. If you then go back to the menu, enable another (different) addon, and click `Done`, your common infected will be fixed (because `FSMAFAS` was allowed to fully execute when you clicked `Done` due to having an addon enabled). So, with this in mind, there were now two goals:

1. Make `FSMAFAS` only execute when addons are enabled in 2.2.0.4+ (and don't cause the same bug present in pre-2.2.0.4 versions)
2. Make `FSMAFAS` not suffer from the faulty logic I just described in pre-2.2.0.4 versions

Achieving #1 is easy - we simply just save the current state of the addon metadata vector member on each execution of our `FSMAFAS` hook, and compare that to the previous value to determine whether we need to call `FSMAFAS` or not (instead of only relying on the current value to determine that, like Valve previously did). 

Achieving #2 is a matter of NOPing out the check for whether addons are enabled - on its own, this change makes `FSMAFAS` always execute in the older versions (just like how it does 2.2.0.4+) which obviously comes with issues of its own, but we can obviously just reuse our hook logic for pre-2.2.0.4 versions to prevent those issues.

### Demonstration of the fix

(actual demonstration with the fix enabled is ~45s into the video - I use z_common_limit to control when the zombies spawn, so that I can control exactly when the stutter will happen)

{{< youtube URRwSBS6jxM >}}

## Conclusion

So, with all of that, we have successfully mitigated the issue. Is the fix perfect? Not quite. A perfect fix would do what we're already doing here, in addition to addressing the scenario where a client reconnecting to a server that hasn't changed its map since it left will experience the hitches. As mentioned before, we'd have to investigate the state of the material cache at various points during the load, see when and what is freeing/restoring it, and try to properly understand why non-host clients don't experience the issue. However - the effort required for that is not worth when this current fix gets us 99% of the way there. As I mentioned earlier, the fact that the engine is doing panicked raw FS searches before eventually locating the files in the VPK might be a good piece of information to work from if someone were to go down that path, though, after some brief testing at the time of writing, I have my doubts as to whether this is meaningful.

Something I didn't failed to explain up to this point is that all of the code/hooks etc was written for the plugin [Source Speedrun Tools](https://mikes.software/sst) (*NOT* to be confused with "Speedrunner Tools" by noa1mbot on the Steam Workshop), which is used by L4D/L4D2 and Portal speedrunners (and eventually even more Source games, most likely).

Anyways, the code for this fix can be found [here](https://github.com/imaciidz/sst/blob/b553e8d3c7c41373e4fa218b3a92213757216fb8/src/l4daddon.c). It has yet to be merged into SST itself, so if you are interested in trying out the fix yourself, you will have to build SST from the source code (instructions for that are on the [project website](https://mikes.software/sst)). Once the code is merged and a release of SST happens, I'll make an update here.

If you are interested in playing the game with this fix, then you should be aware that loading SST **requires** that you launch the game in insecure mode, so you won't be able to play on official Valve servers or any servers running secure mode in general. If this fix was implemented via any other unofficial means, there is still no way you'd be able to use it without insecure mode, unless you wanted a 99.999% guaranteed VAC ban.

Now, to recap, what all did we gain from this?

* The game *should* start up faster when no addons are enabled, since `FSMAFAS` gets called 4 times before you reach the main menu, and each call to `FSMAFAS` wastes about a second or so (at least on my R7 7800X3D - it's probably worse with older hardware)
* Loads in general are quicker (I observed a ~2 second improvement when simply running `map c2m2_fairgrounds` while having all addons disabled).
* In pre-2.2.0.4 game versions, if only one addon is enabled, and you disable that addon, it will now get properly disabled.
* No more hitches caused by materials being late-loaded. This will definitely improve performance on custom maps as well, since again, this isn't just limited to CI population materials - it's seemingly *all* materials.

I'm really happy to have figured this out - the ~2 weeks spent of non stop staring at Ghidra after coming home from work each day paid off, lol. I realize it probably doesn't sound like I spent two weeks doing that, but I chose to exclude most of the 'going nowhere' moments in this writeup for brevity. Huge thanks to bill_play3 for being so helpful and putting up with all of my stupid questions as we worked through all of this. While I did track a lot of this down myself, the end result would not have been possible without his help.

I hope Valve will implement a proper fix one day, but only time can tell.

### lol

As mentioned in the beginning, this took me almost four months to finish writing and publish (started on October 24th). Working over 40 hours a week at a somewhat physically intensive job while also dealing with severe executive dysfunction is quite miserable!

If you made it this far, thanks for reading. ❤️