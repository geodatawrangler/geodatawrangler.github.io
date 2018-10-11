---
layout: post
title: "Down the Emacs Rabbit Hole"
date: 2018-10-10
---
Over the years I have followed the [holy](https://www.gnu.org/fun/jokes/gospel.html) [war](https://en.wikipedia.org/wiki/Editor_war) between [vi](https://en.wikipedia.org/wiki/Vi) and [Emacs](https://en.wikipedia.org/wiki/Emacs) with some interest. As a university student and a GIS Analyst using various versions of Unix, I was handed vi and taught to use it. vi was omnipresent and could be learned to a passable level in a short amount of time. So, I was a vi user. Emacs was alluring though. The old joke, that Emacs is "a great operating system, lacking only a decent editor"[1], was both a draw to and a caution against it's use. I tried it out a few times, but the requirement to learn so many commands with multiple meta keys was too much bother. I would give up after an hour or two, when I had to look up open a file for the eighth time.

At the same time, I was experimenting with various ways of keeping track of my huge backlog of "to do" items. Often hundred's of items long. Tracking it in a Word document, tracking it in an excel spreadsheet. Using tabs in steno note book. When I finally gave up the fight and switched from paper notes to Microsoft OneNote, I started keeping the list in there. It did not work that well. Also, I have an ever growing fear that when Microsoft decides to move from OneNote to the next great thing, all my notes will be locked inside its proprietary file format. I've been burned with that problem in the past.

I became aware of Org Mode. It was built within the platform that is Emacs. It uses plain text files. It has fantastic miraculous sounding capabilities. It can be version controlled with git. I can store it in the cloud. I've become familiar with using Markdown and Jekyll to publish my blogs on GitHub and I love the plain text files being used for markup. So I took a serious stab at using Emacs for the first time.

My initial attempt is partially documented below in _*First Install - Aquamacs*_. While at the same time using it on my Windows 8.1 computer at work. That became frustrating because of the differences associated with Aquamacs. So I've set out to document fully how I got Emacs with Org Mode and all its other wonders in _*Second Install - Homebrew*_.

This post will be a living document. Currently the formatting is rough. I'm using this post as a test-bed for [Magit](https://magit.vc) portion of the content.

## Second Install - Homebrew
1. `brew cask install Emacs`
2. Configure Interface - add items to init.el
    1. Line Numbers:
        + create file `~/.emacs.d/init.el` and add:

            ```(global-linum-mode t)
            ```

    2. Recent Files
        * Add to init.el:

			```(recentf-mode 1)
			(setq recentf-max-menu-items 25)
			(global-set-key "\C-x\ \C-r" 'recentf-open-files)
			```

	3. Visual Line Mode - wraps lines on word breaks
	    * Add to init.el: `(global-visual-line-mode t)`

    4. Configure MELA repository for packages
	    * Add to init.el

			```(require 'package)
            (add-to-list 'package-archives
			             '("melpa-stable" . "https://stable.melpa.org/packages/"))
            (package-initialize)
            ```
	5. [Turn on Parenthesis Matching](https://www.gnu.org/software/emacs/manual/html_node/efaq/Matching-parentheses.html)
	   * Add `(show-paren-mode 1)` to init.el
	
3. Markdown Mode for Emacs
    1. [Install package](https://jblevins.org/projects/markdown-mode/).
       * M-x package-install RET markdown-mode RET
    2. [Pandoc](https://pandoc.org/) was previously installed.
       * Apparently using anaconda.
    3. Add to init.el inside the `(custom-set-variables` section before the final `)`:
       *`'(markdown-command "/Users/paulmccombs/anaconda3/bin/pandoc")`

4. [Org Mode](https://orgmode.org/worg/org-tutorials/orgtutorial_dto.html)
   1. Add to init.el:
	  ```(require 'org)
	  (define-key global-map "\C-cl" 'org-store-link)
	  (define-key global-map "\C-ca" 'org-agenda)
	  (setq org-log-done 'time)
	  ```
   2. For each .org file you want to participate in the Agenda open the file and issue command C-c [
   
   3. Create a [Custom Agenda Command](https://orgmode.org/worg/org-tutorials/org-custom-agenda-commands.html)
   
      * __NOTE__ you can use: M-x customize-variable RET org-agenda-custom-commands
	  
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
   
5. Auto-Complete 
   1. [Install package](https://github.com/auto-complete/auto-complete).
	  * M-x package-install [RET] auto-complete [RET]
   2. [Add to init.el](https://emacs.stackexchange.com/questions/18982/how-do-i-make-auto-complete-enabled-by-default):
	  ```(ac-config-default)
	  (global-auto-complete-mode t)
	  (add-to-list 'ac-modes 'markdown-mode)
	  (add-to-list 'ac-modes 'org-mode)
	  ```
	  
6. [Spell Checking](https://stackoverflow.com/questions/19022015/emacs-on-mac-os-x-how-to-get-spell-check-to-work)
   1. At Terminal Prompt
	  `brew install aspell --with-all-langs`
	  `which aspell`
   3. Return from last prompt similar to `/usr/local/bin/aspell`
   4. Run Customize... from Edit->Spell menu
   5. Set ispell-program-name variable to `/usr/local/bin/aspell` or other return from earlier prompt command.
   2. Run Spell-Check Buffer from Edit->Spell menu
   3. Note: Flyspell can work but needs a functional mouse-2 click which requires hackery for macOS track-pad.

7. [Magit](https://magit.vc/manual/magit/Installing-from-an-Elpa-Archive.html#Installing-from-an-Elpa-Archive): git version control
   1. M-x package-refresh-contents [RET]
   2. M-x package-install [RET] magit [RET]
   3. Test version numbers: M-x magit-version [RET]
	  Result: Magit 2.13.0, Git 2.17.1 (Apple Git-112), Emacs 26.1, darwin
   4. Add a global key binding for magit-status to initl.el
	  ;; Configure magit
	  ;; https://magit.vc/manual/magit/Getting-Started.html#Getting-Started
	  (global-set-key (kbd "C-x g") 'magit-status)

7. [Mu4e](http://cachestocaches.com/2017/3/complete-guide-email-emacs-using-mu-and-/) - e-mail client


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
