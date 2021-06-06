# FAQ

Here is a list of some frequent questions from package maintainers.

## Why am I not seeing the same errors and warnings as you?

One possibility is that you're compiling your code in an instance of Emacs that
has already loaded your code.  Enabling the `flycheck` extension can help you
root these out, or you can restart Emacs using `emacs -Q` and try compiling with
a clean instance.  Another possibility is you are using different versions of
the software than we are.  When we review packages, we target the latest version
of `package-lint` and the latest stable version of Emacs.

## When/why should I write a mode?

In most cases, simply _loading_ a package should not cause any side effects such
as configuring advice or performing network I/O.  (There are a few known
exceptions, such as adding entries to `auto-mode-alist` or
`auth-source-backend-parser-functions`.)  It's better to wait for the user to
explicitly ask for these things to happen by providing entrypoints through
[major and minor
modes](https://www.gnu.org/software/emacs/manual/html_node/elisp/Modes.html).

## How do I make package-lint check a package with multiple files?

See the documentation for `package-lint-main-file`.  One way to make use of this
variable is to add the following somewhere in each of your secondary files (just
make sure that it isn't inside your `;;; Commentary` block):

```elisp
;; Local variables:
;; package-lint-main-file: "my-main-file.el"
;; end:
```

## Should I fix _all_ the errors and warnings?

It's true that some of the checkers will raise warnings over benign issues.  But
when opening a request to add a package to MELPA, please try to fix as many as
you can.  We'll help!

Here are some of the benefits: you won't get used to seeing them, which could
cause you to disregard real issues in the future.  By the same reasoning, it's
better if the users who are installing your package only see byte-compilation
warnings when something is wrong on their side.  The checkers also serve as a
crude but effective means of quality control for MELPA reviewers, and
negotiating "grey areas" tends to take more time (ours and yours) than simply
fixing them.

## How do I make a package containing multiple themes?

Choosing filenames in multi-theme packages can be tricky: typically you should
have a "main" file that shares the same name as your package, and each theme
file should begin with your package prefix while also ending in `-theme.el`.

As a rough guideline to help with this, below is an example of a multi-theme
package called `purple-themes`.  First comes our main file, `purple-themes.el`:
```elisp
;;; purple-themes.el --- Purple themes

;;; Code:
(defgroup purple-themes nil "Purple themes" :prefix "purple-themes-" :group 'faces)
(defvar purple-themes-main-color "#FF00FF")

;;; ###autoload
(add-to-list
 ;; Register themes when this file is loaded or when the buffer is evaluated:
 'custom-theme-load-path
 (if load-file-name (file-name-directory load-file-name) default-directory))

(provide 'purple-themes)
;;; purple-themes.el ends here
```

From here we can define the actual color themes, for example a dark variant.
This file should begin with `purple-themes-` and end with `-theme.el`:
```elisp
;;; purple-themes-dark-theme.el --- Purple themes: dark variant

;;; Code:
(require 'purple-themes)

(deftheme purple-themes-dark "Purple themes: dark variant.")
(custom-theme-set-faces
 'purple-themes-dark
 `(default ((t (:background "midnight blue" :foreground ,purple-themes-main-color)))))

(provide-theme 'purple-themes-dark)
(provide 'purple-themes-dark-theme)
;;; purple-themes-dark-theme.el ends here

;; Local variables:
;; package-lint-main-file: "purple-themes.el"
;; end:
```

<!--
## Can I make MELPA run a command when it builds my package?

To be written.

## How do I rename/split/combine packages?

To be written.
-->
