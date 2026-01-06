# Templates for Obsidian and Templater for use with genealogy research
One of my hobbies is genealogy research; it has been a hobby since the early eighties. Back then, I had to write down everything or make a xerox of it (if even possible). Today, making notes and doing research is way easier when using a program like [Obsidian](https://obsidian.md). And especially when using the extremely powerfull plugin [Templater](https://github.com/SilentVoid13/Templater). Below are the templates I use almost exclusively in my genealogy-vault.

I have to apologise in advance; all texts are in Dutch (my native language)
## Requirements
- [Obsidian](https://obsidian.md)
- [Templater](https://github.com/SilentVoid13/Templater)
- [Commander](https://github.com/phibr0/obsidian-commander) (Optional)
- [Buttons](https://github.com/shabegom/buttons) (Optional)

## How to use them?
Download the templates and copy them to the folder in your vault that you configured as your template-folder. After that, create Templater-shortcuts for each template. You can assign keys to the shortcuts, but that's optional. If you use commander, you can add the Templater commands to the context menu for the editor and the titelbar in Obsidian. This also is optional.

## Templates
| Template | Templater Action | Info |
| --- | --- | --- |
| [onderzoekstaak.md](Onderzoekstaak.md) | Create | A template for a single research task. It allows for entering a title and tags and subdivides the note into several sections |
| [persoonskaart.md](Persoonskaart.md) | Create |  A template for a single individual record. It allows for all details to be entered into frontmatter. Can be updated by inserting [update-persoonskaart.md](update-persoonskaart.md) |
| [Taak - Insert.md](Taak%20-%20Insert.md) | Insert | A template to add a task to the current note (used within [onderzoekstaak.md](Onderzoekstaak.md)) |
| [basis-persoonskaart.md](basis-persoonskaart.md)| Insert | A template containing only the basis frontmatter for a persoonskaart. For example: if you receive notes from somebody else and need to update it to your own standard |
| [status-wijziging.md](status-wijziging.md) | Insert | A small template that allows to you to change the status of a note created with the onderzoekstaak.md (frontmatter: status) |
| [update-onderzoekstaak.md](update-onderzoekstaak.md) | Insert | A template that allows you to insert the researchtask into existing notes |
| [update-persoonskaart.md](update-persoonskaart.md) | Insert | A template that will transform the frontmatter in a note created by [persoonskaart.md](Persoonskaart.md) or containing the [basis-persoonskaart.md](basis-persoonskaart.md) to a fully fledged individual record. |

## Screenshots

#### New Research Task
![Onderzoekstaak](https://github.com/jacobfresco/obsidian-genealogy/blob/main/screenshots/onderzoekstaak-new.png)

#### Research Task in Edit Mode
![Onderzoekstaak Edit Mode](https://github.com/jacobfresco/obsidian-genealogy/blob/main/screenshots/onderzoekstaak-editpng.png)

#### Add a task to a research task
![onderzoekstaak - add task](https://github.com/jacobfresco/obsidian-genealogy/blob/main/screenshots/onderzoekstaak-addtask.png)

#### Research task in read mode
![onderzoekstaak - taskadded](https://github.com/jacobfresco/obsidian-genealogy/blob/main/screenshots/onderzoekstaak-taskadded.png)

#### Individual record
![persoonskaart - edit](https://github.com/jacobfresco/obsidian-genealogy/blob/main/screenshots/persoonskaart-edit.png)

#### Individual record - Reading mode
![persoonskaart - read mode](https://github.com/jacobfresco/obsidian-genealogy/blob/main/screenshots/persoonskaart-read.png)

#### Individual record - update (menu options were added using the Commander-plugin)
![persoonskaart - update](https://github.com/jacobfresco/obsidian-genealogy/blob/main/screenshots/persoonskaart-update.png)
