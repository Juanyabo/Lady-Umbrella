# ☂️ Game description
---
Lady Umbrella is a 3D action-adventure shooter where you play as special agent Francesca De Angelis (Lady Umbrella). After being betrayed and framed by her partner while infiltrating Italy’s last mafia family, she must clear her name by taking down both the mafia and her own agency.

The game combines third-person shooting, shield-based combat, and dynamic traversal mechanics. The main weapon, a shotgun-umbrella, doubles as a shield and integrates various multi-purpose gadgets that support both combat and exploration.
# 👨‍💻 My Contribution
---
Responsible for input device recognition and UI development, including menus, navigation, most HUD elements, and bundled PSOs (Pipeline State Objects) used for the shader compilation loading screen.
- ## ⌨️🖱️/🎮 Input Device Recognition
  During the prototyping and alpha phases, the input device recognition system was implemented as a function within the player class. After every input action, this function retrieved the Device Identifier of the most recently used hardware (keyboard/mouse or XInput controller) and updated the corresponding control scheme icons dynamically, allowing for a smooth transition between input devices. In the beta phase, the system was moved to the game’s custom Controller class, removing its dependency on the player.
```c++
void AMainController::ParseInputDevice()
{
	FPlatformUserId PlayerID = FPlatformUserId::CreateFromInternalId(0);
	
	if (const UInputDeviceSubsystem* InputDeviceSubsystem = UInputDeviceSubsystem::Get())
	{
		FHardwareDeviceIdentifier DeviceIdentifier = InputDeviceSubsystem->GetMostRecentlyUsedHardwareDevice(PlayerID);
		FString DeviceName = DeviceIdentifier.ToString();
		TArray<FString> ParsedDeviceName;
		DeviceName.ParseIntoArray(ParsedDeviceName, TEXT("::"));
		if (ParsedDeviceName.Num() > 0)
		{
			DeviceName = ParsedDeviceName.Last();
		}
		if (!InputDevice.Equals(DeviceName))
		{
			InputDevice = DeviceName;
			if (ULU_GameInstance* GameInstance = Cast<ULU_GameInstance>(UGameplayStatics::GetGameInstance(GetWorld())))
			{
				EPlatform UpdatedPlatform =
					*FWidgetAssets::PlatformsKeys.FindKey(*InputDevice) == EPlatform::PC ?
					EPlatform::PC : EPlatform::PS;
				GameInstance->SetPlatformName(UpdatedPlatform);
			}
		}
	}

	
}
```
- ## 💻 UI
---
- ### Menus
  During the alpha phase, I was responsible for creating the main menu and pause menu. I implemented the Menu class, which handled navigation by storing buttons in an array and managing focus using Unreal Engine functions like NativeOnMouseMove, NativeOnAnalogValueChanged, and NativeOnPreviewKeyDown.

  This system allowed future menus to inherit from the Menu class, only adding button-specific logic. I also developed an animated Button with three states — Hovered, Idle, and Pressed — each updating visually when activated.

  During the beta phase, a colleague updated and refined the menus, including the classes and the animated Button originally created in the alpha, which gave me the opportunity to focus on improving the HUD.

  Here is how the main menu and pause menu looked during the alpha phase, based on the original concept I worked with:

- #### Main Menu (Alpha)
- #### Pause Menu (Alpha)
- #### Chapters Menu
  Displays only the unlocked chapters along with their corresponding images and descriptions. When a chapter button is pressed, the system retrieves the button’s index, looks up the corresponding chapter name from an array, and opens the selected level.
  
  <img width="400" alt="Chapters-menu" src="https://github.com/user-attachments/assets/252fa04a-5a47-46a2-a72f-df6633899493" />
---
- ### HUD
  I developed a custom HUD class that handles the creation and visibility logic of various widgets, allowing the game’s custom controller to manage and display them as needed. The widgets I implemented include the umbrella crosshair, umbrella ammo, grenade indicator, and skip cinematic. They are only displayed when the player is holding the umbrella or during combat except for the skip cinematic widget.
- #### Crosshair
   Receives the umbrella spread and range values and visually represents them.
  
    Changes color when the umbrella is in range to hit an enemy or a destructible object.
  
    Animates after firing to show when the umbrella is ready to shoot again.
  
    Hit markers: one per enemy hit reflecting the number of umbrella pellets, turns red when killing an enemy, white otherwise, and disappears when the umbrella can fire again.
  
    <img width="400" alt="On-destructible" src="https://github.com/user-attachments/assets/149071db-198b-48df-91fd-585b473057ad" />
    <img width="400" alt="On-enemy" src="https://github.com/user-attachments/assets/00b88445-57f2-4d5a-abe8-77b974c29d49" />
    <img width="400" alt="Hit-marker" src="https://github.com/user-attachments/assets/e4ee6b69-725b-4a55-998f-c8dc62dd2c23" />

  - #### Ammo
      Displays the number of bullets via dynamically generated bullet images and numeric display of bullets in reserve.

      Animates when ammo is low to prompt the player to reload.

      Displays unlocked gadgets.

      Visually indicates the selected gadget.

      Shows cooldowns for gadgets.

      Displays a shield with a shader material representing its percentage.

      Hides when full and no longer needed.

      <img width="400" alt="Gadget-unlock" src="https://github.com/user-attachments/assets/738b1500-721c-4846-9fdc-a8cf885f5aff" />
      <img width="400" alt="Gadget-selected" src="https://github.com/user-attachments/assets/efc87d62-917e-47fc-a3e1-4761fe24bac7" />
      <img width="400" alt="low-ammo-animation" src="https://github.com/user-attachments/assets/7e31b14b-8208-46b8-ba9a-0ef7dbc97b48" />
      <img width="400" alt="No-ammo" src="https://github.com/user-attachments/assets/a459cab3-87d3-4bed-87dc-cfbe4e25d0ea" />
      <img width="400" alt="Shield" src="https://github.com/user-attachments/assets/be65f4b4-3bd4-4754-8f45-fe7d7bf3ba84" />

  - #### Grenade Indicator
      Shows the location of grenades thrown by enemies, adjusting depending on whether the grenade is on-screen or off-screen.

      <img width="400" alt="Grenade-on-screen" src="https://github.com/user-attachments/assets/166b51eb-86ea-4dd2-b0ee-fbfb3b433e7f" />
      <img width="400" alt="Grenade-off-screen" src="https://github.com/user-attachments/assets/524aa339-c7a6-4188-9253-7123012ded4f" />

  - #### Skip Cinematic
      Allows the player to skip cinematics by pressing a key (controller or keyboard), displaying the correct key depending on the input device.

      <img width="400" alt="Skip-cinematic" src="https://github.com/user-attachments/assets/50032863-a9da-4b8f-b489-1b4e7fd936b1" />
---
- ### Shaders loading screen
  I implemented the pre-compiled shader screen based on Unreal Engine’s Bundled PSOs documentation. This manual process involves playing through the game and using tools to collect all PSOs that are actually used. These are then bundled into a file that can be loaded at startup, significantly reducing runtime compilation. The number of PSOs is then set as the maximum value of a progress bar within a custom controller, representing how many shaders have been compiled.

  <img width="400" alt="Shaders-screen" src="https://github.com/user-attachments/assets/5bc5c47d-b47a-42e1-b1d6-9617eedfb1e2" />
---
# 👏 Special Thanks
I would like to thank all the playtesters who provided feedback throughout the development of the game, from the earliest prototype to the final version.
My gratitude also goes to everyone at Zulo Interactive, who made this project possible, and to the tutors for their guidance and support.
# 🕹️ Play Lady Umbrella
<br />
<p align="center">
  <img width="616" height="353" alt="Key-art" src="https://github.com/user-attachments/assets/9d3af2f5-1099-49a3-9797-9cd3fe1c2018" />
</p>
<p align="center">
  <a href="https://store.steampowered.com/app/3956890/Lady_Umbrella/" target="_blank">
    <img src="https://img.shields.io/badge/Now%20on%20Steam-1b2838?style=for-the-badge&logo=steam&logoColor=white" alt="Play on Steam">
  </a>
</p>

