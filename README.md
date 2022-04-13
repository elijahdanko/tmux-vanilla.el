Emacs vanilla Tmux switcher

Feature was requested  (https://github.com/tmux/tmux/issues/2904).

# Features

* [C-x o] - switches to a Tmux panel if it's visible, otherwise executes Emacs'
  `other-window`. The pane circling feature works intuitively on both Emacs
  and other Tmux panes (keeping Emacs `other-window` navigation order).
* [C-x 1] - closes other Tmux panes if they're visible, otherwise executes
  Emacs' `delete-other-windows`.
* [C-x 0] - closes current Tmux panes (no-op on the last pane) or executes
  Emacs' `delete-window`.
* [tmux-prefix-key c] - create a new Tmux window preserving Emacs buffer's path
  (defined by 'ttymux-pane-directory-method custom variable).
* [tmux-prefix-key %] - split Tmux pane vertically preserving Emacs buffer's
  path (defined by 'ttymux-pane-directory-method custom variable).
* [tmux-prefix-key "] - split Tmux pane horizontally preserving Emacs buffer's
  path (defined by 'ttymux-pane-directory-method custom variable).

# Minimal tmux.conf

```sh
unbind C-b
set -g prefix C-q
bind C-q send-prefix
is_emacs='echo "#{pane_current_command}" | grep -iqE "emacs"'
is_other_panes='echo "#{window_panes}" | grep -vqwE "1"'
bind-key -T prefix % if "$is_emacs" "send-prefix ; send-keys %" "split-window -h -c \"#{pane_current_path}\""
bind-key -T prefix \" if "$is_emacs" 'send-prefix ; send-keys \"' "split-window -v -c \"#{pane_current_path}\""
bind-key -T prefix c if "$is_emacs" "send-prefix ; send-keys c" "new-window -c \"#{pane_current_path}\""
bind -Temacs-keys o if "$is_emacs" "send C-x; send" "select-pane -t :.+"
bind -Temacs-keys Any { send C-x; send }
bind -Temacs-keys 1 { kill-pane -a; send C-x; send }
bind -Temacs-keys 0 if "$is_emacs" "send C-x; send" 'if $is_other_panes kill-pane'
bind -Troot C-x switch-client -Temacs-keys
```

# Licence

MIT
