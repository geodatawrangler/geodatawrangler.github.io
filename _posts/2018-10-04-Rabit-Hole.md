---
layout: post
title: "Down the Emacs Rabbit Hole"
date: 2019-05-17
---

Over the years I have followed the [holy](https://www.gnu.org/fun/jokes/gospel.html) [war](https://en.wikipedia.org/wiki/Editor_war) between [vi](https://en.wikipedia.org/wiki/Vi) and [Emacs](https://en.wikipedia.org/wiki/Emacs) with some interest. As a university student and a GIS Analyst using various versions of Unix, I was handed vi and taught to use it. vi was omnipresent and could be learned to a passable level in a short amount of time. So, I was a vi user. Emacs was alluring though. The old joke, that Emacs is "a great operating system, lacking only a decent editor"[1], was both a draw to and a caution against it's use. I tried it out a few times, but the requirement to learn so many commands with multiple meta keys was too much bother. I would give up after an hour or two, when I had to look up open a file for the eighth time.

At the same time, I was experimenting with various ways of keeping track of my huge backlog of "to do" items. Often hundred's of items long. Tracking it in a Word document, tracking it in an excel spreadsheet. Using tabs in steno note book. When I finally gave up the fight and switched from paper notes to Microsoft OneNote, I started keeping the list in there. It did not work that well. Also, I have an ever growing fear that when Microsoft decides to move from OneNote to the next great thing, all my notes will be locked inside its proprietary file format. I've been burned with that problem in the past.

I became aware of Org Mode. It was built within the platform that is Emacs. It uses plain text files. It has fantastic miraculous sounding capabilities. It can be version controlled with git. I can store it in the cloud. I've become familiar with using Markdown and Jekyll to publish my blogs on GitHub and I love the plain text files being used for markup. So I took a serious stab at using Emacs for the first time.

My initial attempt is partially documented below in **_First Install - Aquamacs_**. While at the same time using it on my Windows 8.1 computer at work. That became frustrating because of the differences associated with Aquamacs. So I've set out to document fully how I got Emacs with Org Mode and all its other wonders in **_Fresh Install - Homebrew_**.

### This post is a living document:
I'm using this post as a test-bed for the [Magit](https://magit.vc) portion of the content.
* **May 17, 2019** Change the theme and the default font
* **April 7, 2019** Auto Complete Config fix; Add Saving Sessions and CSV-Mode to list of enhancements to explore
* **March 7, 2019** Added list of enhancements to explore, and added a note about Orgzly.
* **January 17, 2019** Figured out how to push-to-deploy.
* **December 1, 2018** Fixed formatting and updated with experience installing Emacs on a completely fresh 2018 Mac Mini with Mojave.

## Fresh Install - Homebrew ##
1. Install Emacs
   1. Install [Homebrew](https://brew.sh/)
   2. At terminal prompt type `brew cask install emacs`
   3. For MacOS 10.14 (Mojave) it is necessary to allow Emacs to control your computer.
	  It will prompt you when it tries to do something that is not yet permitted. This is a setting in the _Security & Privacy_ panel in _System Preferences_ under the _Privacy_ tab with _Accessibility_ selected.
2. Configure Interface - add items to init.el
    1. Line Numbers:
        + create file `~/.emacs.d/init.el` and add:
		  ```
		  (global-linum-mode t)
		  ```
	
    2. Recent Files
        * Add to init.el:
		  ```
		  (recentf-mode 1)
		  (setq recentf-max-menu-items 25)
		  (global-set-key "\C-x\ \C-r" 'recentf-open-files)
		  ```

    3. Visual Line Mode - wraps lines on word breaks
	    * Add to init.el: `(global-visual-line-mode t)`

	4. [Turn on Parenthesis Matching](https://www.gnu.org/software/emacs/manual/html_node/efaq/Matching-parentheses.html)
	   * Add `(show-paren-mode 1)` to init.el

	5. Configure MELPA repository for packages
	   * Add to init.el
	   ```
	   (require 'package)
	   (add-to-list 'package-archives
		   '("melpa-stable" . "https://stable.melpa.org/packages/"))
       (package-initialize)
       ```
	
3. Markdown Mode for Emacs
    1. [Install package](https://jblevins.org/projects/markdown-mode/).
       * M-x package-install [RET] markdown-mode [RET]
    2. [Pandoc](https://pandoc.org/)
       * At Terminal Prompt type `brew install Pandoc`
    3. Add to init.el inside the `(custom-set-variables` section before the final `)`:
       * `'(markdown-command "/usr/local/bin/pandoc")`

4. [Org Mode](https://orgmode.org/worg/org-tutorials/orgtutorial_dto.html)
   1. Add to init.el:
      ```
	  (require 'org)
	  (define-key global-map "\C-cl" 'org-store-link)
	  (define-key global-map "\C-ca" 'org-agenda)
	  (setq org-log-done 'time)
	  ```
    1. For each .org file you want to participate in the Agenda open the file and issue command C-c [
   
    2. Create a [Custom Agenda Command](https://orgmode.org/worg/org-tutorials/org-custom-agenda-commands.html)
    * __NOTE__ you can use: M-x customize-variable [RET] org-agenda-custom-commands
    * Otherwise, add the following text to init.el inside the `(custom-set-variables` section. The "n" option was included already I added the "L" section to do a [2 week agenda](https://emacs.stackexchange.com/questions/12517/how-do-i-make-the-timespan-shown-by-org-agenda-start-yesterday) with log mode turned on.
		```
		'(org-agenda-custom-commands
		(quote
			(("L" "Time (L)ine - Biweekly Report" agenda ""
				(
					(org-agenda-start-day "-14d")
					(org-agenda-span 14)
					(org-agenda-start-on-weekday nil)
					(org-agenda-start-with-log-mode '(closed clock state))
				)
				)
				("n" "Agenda and all TODOs"
					((agenda "" nil)
					(alltodo "" nil))\
				nil))))
	  ```
    3. Org files in the cloud. I have started experimenting with [Orgzly](http://www.orgzly.com) and keeping an org file synced on my Android phone, my laptop, and my desktop. It's working OK so far.

5. Auto-Complete 
   1. [Install package](https://github.com/auto-complete/auto-complete).
	  * M-x package-install [RET] auto-complete [RET]
   2. [Add to init.el](https://emacs.stackexchange.com/questions/18982/how-do-i-make-auto-complete-enabled-by-default):
	  ```
      ;; Set up configuration for auto complete
	  ;; Among other things the first line pops-up doc strings for functions
	  ;;  highlighted in AC's suggestions list, while in Emacs-Lisp mode.
	  (ac-config-default) 
	  (global-auto-complete-mode t)
	  (add-to-list 'ac-modes 'markdown-mode)
	  (add-to-list 'ac-modes 'org-mode)
	  ```
   
6. [Spell Checking](https://stackoverflow.com/questions/19022015/emacs-on-mac-os-x-how-to-get-spell-check-to-work)
   1. At Terminal Prompt
	  ```
	  brew install aspell --with-all-langs
	  which aspell
	  ```
   2. Copy the response from last command. Similar to: `/usr/local/bin/aspell`
   3. Run Customize... from Edit->Spell menu
   4. Set ispell-program-name variable to `/usr/local/bin/aspell` or other return from earlier prompt command.
   5. Run Spell-Check Buffer from Edit->Spell menu
   6. __NOTE__ Flyspell can work but needs a functional mouse-2 click which requires hackery for macOS track-pad.

7. [Magit](https://magit.vc/manual/magit/Installing-from-an-Elpa-Archive.html#Installing-from-an-Elpa-Archive): git version control
   1. M-x package-refresh-contents [RET]
   2. M-x package-install [RET] magit [RET]
   3. Test version numbers: M-x magit-version [RET]
	  ```
	  Magit 2.90.1, Git 2.17.2 (Apple Git-113), Emacs 26.1, darwin
	  ```
   4. Add a global key binding for magit-status to init.el
	  ```
	  ;; Configure magit
	  ;; https://magit.vc/manual/magit/Getting-Started.html#Getting-Started
	  (global-set-key (kbd "C-x g") 'magit-status)
	  ```
   5. __NOTE__ [Regarding push-to-deploy](https://codingitwrong.com/wiki/git-push-to-deploy)
	  One of the things I hoped to accomplish with Magit in emacs is a simple solution for publishing my static hand-crafted [personal website](https://www.lazym8.com). I was struggling because default behavior requires using two branches and then automating the merge to the master branch using [git hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks), which was on my list to figure out, but since I don't ever edit the files on the server, push-to-deploy is more straight-forward. In the remote repository on the web server you only need to configure git with `git config receive.denyCurrentBranch updateInstead`  
   6. __NOTE__ I have not yet learned how to clone a repository with Magit

8. Time to tryout a different look: Change Default Font and Theme
   I elected to use the handy GUI interface for this as I did it originally on my work computer (Windows) quickly while on break.
   1. Download the hipster coding font of your choice. I ended up downloading Fira Mono and Ubuntu Mono from [Google Fonts](https://fonts.google.com/?category=Monospace). Install them in the usual fashion for your [operating](https://support.apple.com/en-us/HT201749) [system](https://answers.microsoft.com/en-us/windows/forum/windows_10-start/installing-new-fonts-on-windows-10/09e5d7c3-0870-4998-adb9-fc5391e0fb49).
   2. Click on _Set Default Font..._ on the _Options_ menu and find the font you chose on the list.
   3. Click on _Custom Themes_ on the _Customize Emacs_ sub-menu on the _Options_ menu and browse the themes listed there. I selected _leuven_ because of how it renders an org mode document. After you decide which one to keep click the _Save Theme Settings_ button. __Just kidding!__ That worked on my Windows 10 Computer at work, but not on my Mac. I found [some directions on Stack Overflow](https://stackoverflow.com/questions/1257426/emacs-mac-osx-and-changing-default-font), which were slightly dated.  
   ![org mode with leuven theme](/images/leuven_org_mode.png)
   4. These Directions worked on MacOS for me:
	  1. Type: M-x customize-face [RET]
	  2. At "Customize face (...):" prompt type: default [RET]
	  3. Edit _Font Family_ value to match what you see in the _Fonts_ dialog. In my case "Fira Mono".  
	  ![_Fonts_ Dialog](/images/fonts.png)
	  5. Uncheck all of the other options. If you do not those settings will override the settings in any subsequent theme that you choose in the _Custom Themes_ buffer.
	  4. Click the _Apply and Save__ button. This will make a change in your init.el file like this:
		 ```
		 (custom-set-faces
		 ;; custom-set-faces was added by Custom.
		 ;; If you edit it by hand, you could mess it up, so be careful.
		 ;; Your init file should contain only one such instance.
		 ;; If there is more than one, they won't work right.
		 '(default ((t (:family "Fira Mono")))))
		 ```
	  5. Exit the buffer using the graphic button in he tool bar at the top of the screen.
9. Topics to explore in the future:
    1. [CSV-Mode](https://www.emacswiki.org/emacs/CsvMode) - Allows column alignment and sorting by column. Also transposing columns and rows.

	9. [Saving Sessions](https://www.gnu.org/software/emacs/manual/html_node/emacs/Saving-Emacs-Sessions.html) This looks very helpful to get figured out, so I don't have to keep loading the same files over and configuring my windows.

	8. [GNUS](https://www.gnu.org/software/emacs/manual/html_node/gnus/index.html) - research for use as e-mail client. Rejected Mu4e as too complicated.

	9. [Elfeed](https://github.com/skeeto/elfeed) - RSS Feed Reader

	10. [Bookmark Plus](https://www.emacswiki.org/emacs/BookmarkPlus#toc60)

	11. [Buffer Menu](https://www.emacswiki.org/emacs/BufferMenu)

	13. [Abbrev Mode](https://www.emacswiki.org/emacs/AbbrevMode) 

## First Install - Aquamacs ##
### Things installed or configured ###
* line numbers
* word wrap
* auto-complete
* org-mode
* markdown-mode
* pandoc

## Config customization ##

_/Users/paulmccombs/Library/Preferences/Aquamacs Emacs/Preferences.el_


```
;; This is the Aquamacs Preferences file.
;; Add Emacs-Lisp code here that should be executed whenever
;; you start Aquamacs Emacs. If errors occur, Aquamacs will stop
;; evaluating this file and print errors in the *Messages* buffer.
;; Use this file in place of ~/.emacs (which is loaded as well.)
(require 'org)
(define-key global-map "\C-cl" 'org-store-link)
(define-key global-map "\C-ca" 'org-agenda)
(setq org-log-done t)
(setq org-agenda-files (list "~/org/work_org_mode/work.org"))
```

_/Users/paulmccombs/Library/Preferences/Aquamacs Emacs/customizations.el_

```
;; Added by Package.el.  This must come before configurations of
;; installed packages.  Don't delete this line.  If you don't want it,
;; just comment it out by adding a semicolon to the start of the line.
;; You may delete these explanatory comments.
(package-initialize)

(custom-set-variables
 ;; custom-set-variables was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(ac-modes
   (quote
    (emacs-lisp-mode lisp-mode lisp-interaction-mode slime-repl-mode nim-mode c-mode cc-mode c++-mode objc-mode swift-mode go-mode java-mode malabar-mode clojure-mode clojurescript-mode scala-mode scheme-mode ocaml-mode tuareg-mode coq-mode haskell-mode agda-mode agda2-mode perl-mode cperl-mode python-mode ruby-mode lua-mode tcl-mode ecmascript-mode javascript-mode js-mode js-jsx-mode js2-mode js2-jsx-mode coffee-mode php-mode css-mode scss-mode less-css-mode elixir-mode makefile-mode sh-mode fortran-mode f90-mode ada-mode xml-mode sgml-mode web-mode ts-mode sclang-mode verilog-mode qml-mode apples-mode markdown-mode gfm-mode org-mode)))
 '(aquamacs-additional-fontsets nil t)
 '(aquamacs-customization-version-id 308 t)
 '(aquamacs-tool-bar-user-customization nil t)
 '(global-auto-complete-mode t)
 '(global-linum-mode t)
 '(global-visual-line-mode t)
 '(markdown-command "pandoc")
 '(ns-tool-bar-display-mode (quote both) t)
 '(ns-tool-bar-size-mode (quote regular) t)
 '(package-selected-packages (quote (auto-complete auto-correct markdown-mode)))
 '(text-mode-hook nil)
 '(visual-line-mode nil t))
(custom-set-faces
 ;; custom-set-faces was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 )
```

### Foot Notes ###
[1] [Editor war, _Wikipedia, The Free Encyclopedia_](https://en.wikipedia.org/w/index.php?title=Editor_war&oldid=862368474) (last visited Oct. 11, 2018).
