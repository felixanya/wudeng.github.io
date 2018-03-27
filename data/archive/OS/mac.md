

## Finder

标题栏显示完整路径：
```
defaults write com.apple.finder _FXShowPosixPathInTitle -bool true; killall Finder
```

显示所有文件
```
defaults write com.apple.Finder AppleShowAllFiles YES
```

Command + Shift + G 可以跳转到指定目录，包括隐藏目录 比如指定intelliJ Idea的sdk时需要用到。


xcrun error: xcode-select --install
