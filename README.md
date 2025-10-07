# epl-project-template
This LabVIEW project is a generic template for developing applications to control stimulus generation and data acquisition especially (but not exclusively) using National Instruments hardware. It incorporates and illustrates many conventions that have evolved over the years and which underlie most of the specialized applications running in EPL. 

## Table of Contents
1. [Some general principles](#some-general-principles)
2. [Setting up LabVIEW](#setting-up-labview)
3. [Starting a new project](#starting-a-new-project)
4. [Working with the project](#working-with-the-project)
5. [Building the project](#building-the-project)
6. [Creating an installer](#creating-an-installer)

## Some general principles
Two important considerations in developing and deploying applications in a research setting are: leaving a Paper trail and Protecting the users. 
- Consistent use of source control (e.g.: git or SVN) is the basis of the **Paper trail**. When new versions are deployed for use in the lab, the repository should be tagged using [semantic versioning](https://semver.org/), and the logs/data files produced by the application should include the semantic version. This makes it possible in the future, for any piece of data, to go back and understand _exactly_ how it was acquired. Such questions inevitably arise from time to time.
- The application should always be deployed in executable (.exe) form, preferably using an [installer](#creating-an-installer). This serves multiple purposes. Built applications do not require a LabVIEW license to deploy. It also makes it possible to quickly revert to a previous version in the event a new version has bugs or there are otherwise questions about its functionality. A good installer makes it simple for any user to update the application on their own, without relying on someone to come set it up for them. This is especially important when the installation requires support files (e.g.: config files or TDT circuits) or additional actions (e.g.: registry configuration or creating a data folder hierarchy).
- Building executable applications also helps **Protect the user** from the temptation to tweak the code while using it, which can have unpredictable effects and leaves no traceable paper trail. (From time to time in my own experiments, I would edit the MATLAB code to fix a bug or hack a new feature, and it _always_ caused problems, usually sooner than later.)
- An obviously important part of protecting the user is designing the application so the user can easily understand how to do what they want while preventing them from inadvertently doing things they don't want to do, but such considerations are beyond the scope of this summary.

### Brief description of semantic versioning
I've come late to this convention, but really appreciate the clarity it provides. Briefly, for V _major_._minor_._patch_:
1. **major** changes break backwards compatibility. This is not a step to be undertaken lightly, but I rarely find it necessary.
2. **minor** changes add functionality without (in theory) breaking anything
3. **patch** changes fix bugs in minor releases

### Maintain the changelog
I've also come late to the practice of maintaining a good changelog, though I've long realized its importance. A good discussion of changelogs can be found [here](https://keepachangelog.com/en/1.1.0/) and an in-house example [here](https://github.com/EPL-Engineering/epl-vitals/blob/main/CHANGELOG.md). Briefly, the changes introduced in every new release should be described _succinctly_, headed by the release's semantic version number and release date. I most commonly use the following change group headers:
- Added
- Changed
- Fixed

I have begun installing the changelog with the application itself.

### A note on repository names
- I like to start each repository name with a string indicating the general lab area for which the application exists, e.g.: 'epl' for general use, 'jenks', or 'ch2'
- People apparently argue about this, but I subscribe to the convention that prefers dashes to underscores so they are not hidden in hyperlinks

## Setting up LabVIEW
### Install EPL VI Library
The EPL VI Library has utilities for a huge number of common tasks. It is essential for editing existing code. The repository can be cloned or copied from GitHub: https://github.com/EPL-Engineering/epl-vi-lib

LabVIEW is fussy about relative file locations when sorting out dependencies. To avoid much heartache, be consistent with folder hierarchies. For example, 
- I maintain all of my code in `D:\Development`. This name and location are arbitrary, but pick one and stick with it. Keep everything together there. The EPL VI Library clone is maintained one level below that in `D:\Development\epl-vi-lib`. Note that while you can clone a repository using any folder name without breaking source control, the folder name **must** be `epl-vi-lib` to avoid breaking LabVIEW dependencies.
- All application specific project repositories are maintained in parallel folders, e.g.: `C:\Development\epl-project-template`

 ### Add EPL VI Library to palette set
 1. From the menu bar in any LabVIEW window, select "Tools > Advanced > Edit palette Set..."
 2. On the **Functions** palette, 
    - right-click and select "Insert > Subpalette..."
    - on the Insert Subpalette dialog, select "Link to an existing palette file (.mnu) and press OK.
    - in the file dialog, navigate to `epl-vi-lib\Utility VIs\Utilities.mnu` and press OK.
    - repeat the previous steps and add `epl-vi-lib\PXI DAQ VIs\Top Level VIs\PXI DAQ VIs.mnu`
 3. On the **Controls** palette, perform the same steps to add `epl-vi-lib\Utility VIs\Controls.mnu`
 
### Customizing LabVIEW
These steps are more optional but still recommended. From the menu bar in any LabVIEW window, 
1. Select "Tools > Options...".
2. Select the Block Diagram category
   - Under General, uncheck "Place front panel terminals as icons" (_icons are a waste of space_)
   - Under tip Strips and Labeling, set "control terminals and constants" label position to "Left-middle" and "indicators" label position to "Right-middle" (_makes block diagrams neater and the flow more obvious_)
3. Select the "Controls/Functions Palettes" category and set Palette to "Category (Standard)" (_for compactness_)

## Starting a new project
This is the best way to use this template to start a new project:
1. Create a source control repository for the project. (See [note on repository names](#a-note-on-repository-names))
2. Clone the new repository to the development folder.
3. **Download the epl-project-template repository as a ZIP**. [(Link here)](https://github.com/keh38/epl-project-template/archive/refs/heads/main.zip) Do not clone it! It needs to become an independent part of the newly created project.
4. Copy the contents of the `epl-project-template-main` folder inside the ZIP to the new project folder.
5. Make the intial commit to source control. 

### Folder organization
The conventional folder hierarchy is shown below.
   - Note some of the folders (e.g.: `Build` and `Installer\Output`) are ignored in source control and won't appear until the project has been built for the first time.
   - the LabVIEW project (.lvproj) is in the root project folder, above the `LV Source` subfolder
   - the `LV Source` folder has been added to the LabVIEW project as an auto-populating folder so that anything added to the folder hierarchy automatically becomes a part of the project
   - the only file in the `LV Source` folder is the application's main VI.
   - the rest of the VIs are in subfolders, organized by type. More complicated projects typically introduce additional subfolders to keep things clearly organized.
```
new-project-folder
│   .gitignore
│   CHANGELOG.md
│   epl-project-template.aliases
│   epl-project-template.lvlps
│   epl-project-template.lvproj
│   README.md
│
├───Build
│
├───Images
│       window_application.ico
│
├───Installer
│   │   epl-project-template-installer.iss
│   │
│   └───Output
│
└───LV Source
    │   epl-project-template.vi
    │
    ├───Sub VIs
    │       EPT-Example Dialog.vi
    │
    └───Typedefs
            EPT-Main Menu.rtm
            EPT-Parameters.ctl
            EPT-State.ctl
```

### Customize the project
1. Choose a short string (2-5 characters) as a "prefix" for the project. It's usually an initialism (e.g.: here we use "EPT" for "epl-project-template") or the name of the application, if short enough (e.g.: "Tosca" was chosen as an app name in part because it is short enough to serve as its own prefix).
   - **Append the prefix to the name of every sub VI and custom control in the project.** For example, in the folder tree above, we have `EPT-Example Dialog.vi` and `EPT-Parameters.ctl`. As with repository names, I prefer dashes to underscores.
   - **Why is the prefix important?** At this point, there are probably 20 or 25 projects containing an "Initialize Hardware" VI. While LabVIEW keeps them straight by folder location (generally, except when refactoring), for a human observer it can be soul-crushing to see 20 files with the same name listed in a search and not have any clue which is which. The prefix makes it immediately obvious which things belong together, e.g: `EPT-Initialize Hardware.vi` and `Tosca-Initialize Hardware.vi`. 
2. Rename the LabVIEW project (.lvproj). I have begun using the repository name as the LabVIEW project name as well, but that's not essential.
3. Rename the LabVIEW sub VIs and controls using the new prefix. **Important, now and forever**: initiate file renaming _from within the LabVIEW project_. Right-click on the item name in the Project Explorer and select "Rename". This way, LabVIEW will automatically update all the dependencies. If you simply rename project files in the Windows File Explorer, arrows will break and chaos will ensue.
4. Create an icon template to clearly identify the sub VIs and custom controls belonging to the project. I use the default "banner" icon template, choose a color for the banner (not white: too plain, not yellow: indicates utility VIs), then use the prefix as the banner title. This plain icon can be saved as a template which can easily be applied to sub VIs and custom controls. 

![alt text](https://github.com/keh38/epl-project-template/blob/main/Images/icon-template.png)

The body of the sub VI icons should contain the name of the VI (or some reasonable abbreviation that will fit), centered horizontally and vertically, in 9-point font using all caps.  It is enormously helpful to look at a block diagram and instantly see which sub VIs are part of the project under development and which belong to something else.

![alt text](https://github.com/keh38/epl-project-template/blob/main/Images/example-icon.png)

## Working with the project
This simple project template illustrates the basic principles by which virtually all of the EPL LabVIEW-based applications are organized. It is well annotated, so the best approach is to open `epl-project-template.vi` (or whatever it has been renamed to) and look through it carefully. Of course, the annotations can be deleted as the project is developed.

Scattered through the code are several examples of the many utilities in the EPL VI Library. It is beyond the scope of this overview to delve into the details of those utilities.

### A few conventions
#### Front panel
These suggestions are purely cosmetic, although I think it does reduce the burden on the user (and developer!) to maintain a consistent appearance across applications. This project template has already been configured according to these conventions.
- **Main window appearance** (Control-i, select "Window Appearance" category)
   - Choose an appropriate window title, then under Customize...
   - hide the scroll bars and uncheck "Allow user to resize window". This ensures the panel appears to the user exactly as designed.
   - only "Show menu bar" if using a customized main menu (as this template does)
   - uncheck "Show toolbar when running"
- I'm partial to using System-style controls on the front panel for their clean, Windows-like look.

![alt text](https://github.com/keh38/epl-project-template/blob/main/Images/System-controls.png)

- I'm similarly partial to using the Silver style push buttons

![alt text](https://github.com/keh38/epl-project-template/blob/main/Images/Silver-buttons.png)

- I like to customize the push buttons with images that represent their function. It looks nice but also makes them easier to identify. There is a library of images and icons that I will put on the network at some point.

- The preceding applies _only_ to the main panel and dialog VIs. For sub VIs that do not appear on screen, just use the standard LabVIEW "modern" controls.

#### Block diagram
These suggestions are more substantive because they improve the readability of the code and with it, the clarity of thinking and design that leads to accurate and robust code in the first place. The [book by Blume](https://www.bloomy.com/labview/labview-style-book) is a bit dated now but has many good ideas. I have a copy in my office. There are other resources [online](https://labviewwiki.org/wiki/LabVIEW_style_guide).

Here are a few suggestions to get started:
- Factor!!! No different than writing code in text, the project should be broken up into sub VIs, each of which _does only one thing_. Nothing improves the fluidity of design and maintainability of the end product more than properly factoring the code. Factor!!!
- Block diagrams should fit on one screen (i.e.: it should be possible to view the entire block diagram without scrolling). If it doesn't fit, it probably needs to be refactored. The only exception is the main VI, which often contains a large number of threads. Nevertheless, each thread by itself should be visible all at once.
- Block diagram flow should be left to right, and if needed top to bottom. Wires should _never_ go to the left and as much as possible not go toward the top. It gets complicated and at some point it becomes more trouble than it's worth to obsess over. But for all that's holy, _never_ go to the left.
- Use clusters liberally to group related variables, to minimize the number of input/output terminals needed and the number of wires flowing through the diagram. Clusters should be [strict type definitions](https://knowledge.ni.com/KnowledgeArticleDetails?id=kA00Z0000019KnUSAU&l=en-US) so that changes to them automatically propagate through the entire project.
- As much as possible, isolate interaction with the front panel controls in a single event structure running in its own thread. Interacting with controls outside of that leads to all sorts of insidious behavior that can be difficult to track down, especially violating the autonomy of the actual stimulus generation/data acquisition threads and causing time-sensitive operations to fail. This is not a hard and fast rule, but the exceptions must be consciously considered and implemented carefully.

Finally, one cosmetic block diagram convention:
- I prefer to use this connection pattern for all subVIs. Five inputs and outputs on the left and right sides is usually sufficient. I find four (the default) is often too few. When all the sub VIs have the same pattern, it means the wires will flow straight between them.

![alt text](https://github.com/keh38/epl-project-template/blob/main/Images/connection-pattern.png) 

## Building the project
An executable build specification has been created for the project template. Some properties need to be customized. Expand the "Build Specifications" node in the LabVIEW project explorer, right-click "EPL Project Template" and select "Properties".
1. **Information**: change the "Build specifiation name" and choose an appropriate "Target filename" for the executable. Make sure the "Destination directory" points to the `Build` subfolder of the main project folder. I increasingly find it annoying to have spaces in the executable filename. I recommend not using them or replacing them with underscores or dashes.

![alt text](https://github.com/keh38/epl-project-template/blob/main/Images/build-info.png)

2. **Source Files**: the main VI must be selected under "Startup VIs". I have been deploying the [changelog](#maintain-the-changelog) by moving it to "Always Included". Be sure to edit the changelog before building the executable.

![alt text](https://github.com/keh38/epl-project-template/blob/main/Images/build-source-files.png)

3. **Destinations**: both the "Application.exe" and "Support Directory" destination paths should point to the `Build` subfolder.

![alt text](https://github.com/keh38/epl-project-template/blob/main/Images/build-destinations.png)

4. **Icon**: (optional). I like to add a unique custom icon to each project, so their shortcuts are easily recognized on the Windows desktop. The icon file (.ico) goes in the `Images` subfolder under the main project folder and then is added manually to the project. Then, unclick "Use the default LabVIEW icon file" and select the custom icon. 

![alt text](https://github.com/keh38/epl-project-template/blob/main/Images/build-icon.png)

5. **Version Information**: unclick "Auto-increment version on build" and set the [semantic version](#brief-description-of-semantic-versioning) here. Left to right, the four boxes are _major_, _minor_, _patch_, and _build_. Build is not used and can be left at zero.

![alt text](https://github.com/keh38/epl-project-template/blob/main/Images/build-version.png)

6. Press "Build" to create the executable.

Note that steps 1-4 only need be done once and the build process usually consists of simply executing steps 5 and 6.

## Creating an installer
[Inno Setup](https://jrsoftware.org/isdl.php) is used to create installers. It has enormous power and accomodates quite sophisticated install procedures, although our needs are typically quite straightforward. Download and install Inno Setup before continuing.

The project template contains an Inno Setup file (`Installer\epl-project-template-installer.iss`). Rename it to something project appropriate and then open it. The file is annotated and indicates near the top where things need to be customized.
