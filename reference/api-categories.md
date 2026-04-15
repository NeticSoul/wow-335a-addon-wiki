---
title: API Categories
type: reference
source_chapters: [27, 28]
created: 2026-04-15
updated: 2026-04-15
tags: [api, categories, reference]
---

# API Categories

Over 1000 C and FrameXML functions organized by domain. Full signatures in Ch 27; this page lists every category from Ch 28.

## Categories (Alphabetical)

| Category | Key Functions (examples) |
|----------|--------------------------|
| Achievement | `GetAchievementInfo`, `GetAchievementLink`, `AddTrackedAchievement` |
| Action | `HasAction`, `UseAction`, `GetActionInfo`, `IsUsableAction` |
| ActionBar | `ChangeActionBarPage`, `GetActionBarPage`, `SetActionBarToggles` |
| Addon-related | `GetAddOnInfo`, `LoadAddOn`, `SendAddonMessage`, `EnableAddOn` |
| Arena | `GetArenaTeam`, `ArenaTeamRoster`, `ArenaTeamInviteByName` |
| Auction | `QueryAuctionItems`, `GetAuctionItemInfo`, `PlaceAuctionBid` |
| Bank | `GetNumBankSlots`, `PurchaseSlot`, `BankButtonIDToInvSlotID` |
| Barbershop | `GetBarberShopStyleInfo`, `SetBarberShopAlternateFormFrame` |
| Battlefield | `GetBattlefieldScore`, `JoinBattlefield`, `LeaveBattlefield` |
| Blizzard Internal | *(restricted — not for addon use)* |
| Buff | `UnitBuff`, `UnitDebuff`, `CancelUnitBuff` |
| CVar | `GetCVar`, `SetCVar`, `GetCVarBool`, `RegisterCVar` |
| Calendar | `CalendarOpenEvent`, `CalendarGetDayEvent`, `CalendarNewEvent` |
| Camera | `CameraZoomIn/Out`, `FlipCameraYaw`, `SetView`, `SaveView` |
| Channel | `JoinChannelByName`, `LeaveChannelByName`, `SendChatMessage` |
| Chat | `ChatFrame_AddMessage`, `ChatEdit_InsertLink`, `GetChatWindowInfo` |
| Class Resource | `GetComboPoints`, `GetRuneCooldown`, `GetRuneType` |
| Client Control/Info | `GetBuildInfo`, `GetLocale`, `GetFramerate`, `GetScreenWidth` |
| Combat | `UnitAttackSpeed`, `UnitDamage`, `UnitRangedDamage` |
| CombatLog | `CombatLogGetCurrentEventInfo`, `CombatLogResetFilter` |
| Companion | `GetCompanionInfo`, `CallCompanion`, `SummonRandomCritter` |
| Complaint | `ComplainChat`, `GetComplaintStatus` |
| Container | `GetContainerItemInfo`, `GetContainerNumSlots`, `UseContainerItem` |
| Currency | `GetCurrencyInfo`, `GetCurrencyListSize` |
| Cursor | `GetCursorInfo`, `ClearCursor`, `PickupItem`, `PickupSpell` |
| Debugging/Profiling | `debugstack`, `debugprofilestart/stop`, `GetFunctionCPUUsage` |
| Duel | `AcceptDuel`, `CancelDuel`, `StartDuel` |
| Equipment Manager | `GetEquipmentSetInfo`, `SaveEquipmentSet`, `UseEquipmentSet` |
| Faction | `GetFactionInfo`, `GetNumFactions`, `ExpandFactionHeader` |
| GM Survey | `GMSurveyQuestion`, `GMSurveySubmit` |
| GM Ticket | `GetGMTicket`, `NewGMTicket`, `DeleteGMTicket` |
| Glyph | `GetGlyphSocketInfo`, `SetGlyph`, `GetNumGlyphSockets` |
| Guild Bank | `GetGuildBankItemInfo`, `GetGuildBankTabInfo`, `DepositGuildBankMoney` |
| Guild | `GuildRoster`, `GetGuildRosterInfo`, `GuildInvite`, `IsInGuild` |
| Hyperlink | `GetItemInfo`, `GetSpellLink`, `GetAchievementLink` |
| In-game Movie | `MovieFrame_PlayMovie` |
| Inspect | `InspectUnit`, `NotifyInspect`, `GetInspectSpecialization` |
| Instance | `GetInstanceInfo`, `GetInstanceDifficulty`, `ResetInstances` |
| Inventory | `GetInventoryItemLink`, `EquipItemByName`, `GetInventorySlotInfo` |
| Item Text | `ItemTextGetItem`, `ItemTextGetPage` |
| Item | `GetItemInfo`, `GetItemQualityColor`, `GetAuctionItemSubClasses` |
| Keybind | `GetBindingKey`, `SetBinding`, `SetBindingClick`, `SaveBindings` |
| Keyboard | `IsShiftKeyDown`, `IsControlKeyDown`, `IsAltKeyDown` |
| Knowledge-base | `KBQuery`, `KBArticle_GetData` |
| Limited Play Time | `PartialPlayTime`, `NoPlayTime` |
| Locale-specific | `GetLocale`, `DeclineName` |
| Looking For Group | `LFGTeleport`, `GetLFGQueueStats`, `SetLFGRoles` |
| Loot | `GetLootSlotInfo`, `LootSlot`, `GetNumLootItems`, `CloseLoot` |
| Lua Library | Standard Lua 5.1 (with restrictions) + WoW additions |
| Mac Client | Platform-specific functions |
| Macro | `GetMacroInfo`, `CreateMacro`, `EditMacro`, `GetNumMacros` |
| Mail | `GetInboxHeaderInfo`, `SendMail`, `TakeInboxItem` |
| Map | `SetMapToCurrentZone`, `GetPlayerMapPosition`, `GetMapZones` |
| Merchant | `GetMerchantItemInfo`, `BuyMerchantItem`, `GetRepairAllCost` |
| Modified Click | `IsModifiedClick`, `GetModifiedClick`, `SetModifiedClick` |
| Money | `GetMoney`, `MoneyFrame_Update`, `GetCoinTextureString` |
| Movement | `MoveForwardStart/Stop`, `StrafeLeftStart/Stop`, `ToggleAutoRun` |
| NPC Gossip Dialog | `GetGossipOptions`, `SelectGossipOption`, `CloseGossip` |
| Objectives Tracking | `AddQuestWatch`, `RemoveQuestWatch`, `GetQuestWatchInfo` |
| Party | `InviteUnit`, `UninviteUnit`, `LeaveParty`, `ConvertToRaid` |
| Pet Stable | `GetStablePetInfo`, `SetPetSlot` |
| Pet | `PetAttack`, `PetFollow`, `PetPassiveMode`, `HasPetUI` |
| Petition | `GetPetitionInfo`, `SignPetition`, `OfferPetition` |
| Player Information | `GetPlayerInfoByGUID`, `UnitName`, `UnitClass`, `UnitLevel` |
| PvP | `GetBattlefieldFlagPosition`, `IsPVPTimerRunning` |
| Quest | `GetQuestLogTitle`, `AcceptQuest`, `CompleteQuest`, `GetQuestReward` |
| Raid | `GetRaidRosterInfo`, `SetRaidTarget`, `ConvertToRaid`, `PromoteToAssistant` |
| Recruit-a-friend | `SummonFriend`, `GrantLevel` |
| Secure Execution | `issecurevariable`, `securecall`, `hooksecurefunc`, `InCombatLockdown` |
| Skill | `GetNumSkillLines`, `GetSkillLineInfo` |
| Social | `GetNumFriends`, `AddFriend`, `GetFriendInfo`, `SetWhoToUI` |
| Socketing | `GetExistingSocketInfo`, `GetNewSocketInfo`, `SocketInventoryItem` |
| Sound | `PlaySound`, `PlaySoundFile`, `StopSound`, `GetMusicPlaying` |
| Spell | `GetSpellInfo`, `CastSpellByName`, `GetSpellCooldown`, `IsSpellInRange` |
| Stance/Shapeshift | `GetShapeshiftForm`, `GetNumShapeshiftForms`, `CastShapeshiftForm` |
| Stat Information | `GetMastery`, `GetCritChance`, `GetDodgeChance`, `GetBlockChance` |
| Summoning | `ConfirmSummon`, `CancelSummon`, `GetSummonConfirmTimeLeft` |
| Talent | `GetTalentInfo`, `GetNumTalentTabs`, `GetTalentTabInfo`, `LearnTalent` |
| Targeting | `TargetUnit`, `ClearTarget`, `AssistUnit`, `TargetNearestEnemy` |
| Taxi/Flight | `TaxiNodeName`, `TakeTaxiNode`, `GetNumRoutes` |
| Threat | `UnitThreatSituation`, `UnitDetailedThreatSituation` |
| Tracking | `GetNumTrackingTypes`, `GetTrackingInfo`, `SetTracking` |
| Trade | `AcceptTrade`, `CloseTrade`, `SetTradeItem`, `GetTradePlayerItemInfo` |
| Trade Skill | `GetTradeSkillInfo`, `DoTradeSkill`, `GetTradeSkillNumReagents` |
| Trainer | `GetTrainerServiceInfo`, `BuyTrainerService`, `GetNumTrainerServices` |
| Tutorial | `TriggerTutorial`, `ClearTutorials` |
| UI/Visual | `UIFrameFadeIn/Out`, `SetDesaturation` |
| Unit | `UnitHealth`, `UnitHealthMax`, `UnitPower`, `UnitAura`, `UnitGUID` |
| Utility | `GetTime`, `date`, `time`, `random`, `format`, `strsplit`, `tinsert` |
| Vehicle | `UnitInVehicle`, `EjectPassengerFromSeat`, `VehicleAimIncrement` |
| Video | `GetScreenResolutions`, `SetScreenResolution`, `RestartGx` |
| Voice | `GetVoiceStatus`, `VoiceChat_StartCapture`, `IsVoiceChatEnabled` |
| Zone Information | `GetZoneText`, `GetSubZoneText`, `GetRealZoneText`, `GetMinimapZoneText` |

## See Also
- [Ch 27 — API Reference](../chapters/ch27-api-reference.md)
- [Ch 28 — API Categories](../chapters/ch28-api-categories.md)
- [WoW API](../concepts/wow-api.md)
