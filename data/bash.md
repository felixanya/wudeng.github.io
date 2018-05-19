# bash


for f in $(ack -f); do 
    encoding=$(file -b --mime-encoding $f)
    if [ "$encoding" == "iso-8859-1" ]; then 
        echo $f; 
        iconv -f gb2312 -t utf-8 --add-signature $f | sponge $f
    fi
done

data/meld_effect.csv
server/core/clib/lworld.c