* Generate thumbnails: `for i in *.jpeg(:r); do ffmpeg -i "$i.jpeg" -vf scale=320:-1 "$i-thumb.jpg"; done;`
