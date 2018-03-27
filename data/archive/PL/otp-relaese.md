# Erlang新特性



rebar3 需要17.4以上。


new purge strategy

## OTP 升级

> The default encoding for Erlang source files was changed from Latin-1 to UTF-8 in Erlang OTP 17.0.

所以从16升到17以上的时候，如果源文件中包含中文等字符，要么在文件头指定文件的编码格式为Latin-1, 要么将所有中文字符串改成 <<"中文"/utf8>> 这种形式。
这个会带来新的问题，原来是list的，现在变成binary了。所以对应的接口也要改掉。


http://erlang.org/documentation/doc-6.0/doc/reference_manual/character_set.html

https://www.metabrew.com/article/a-million-user-comet-application-with-mochiweb-part-1
https://www.metabrew.com/article/a-million-user-comet-application-with-mochiweb-part-2
https://www.metabrew.com/article/a-million-user-comet-application-with-mochiweb-part-3
