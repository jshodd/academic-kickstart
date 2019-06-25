---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "GoChat"
summary: "A realtime chat server and client built with the purpose of learning more about network programming, concurrency, and encryption"
authors: ["jshodd"]
tags: ["go"]
categories: ["programming"]
date: 2019-06-25T14:49:31-04:00

# Optional external URL for project (replaces project detail page).
external_link: ""

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
# links:
# - name: Follow
#   url: https://twitter.com
#   icon_pack: fab
#   icon: twitter

url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: ""
---

![Client image](./GoChat-Client.png)

[See the latest code here](https://www.github.com/jshodd/goChat)


This project was a great learning opportunity for myself, as it was my first project in GoLang and it also gave me a chance to experiment with new concepts like concurrency, network programming, and encryption. This program utilizes Go's included concurrency to accept multiple connections independantly, this was a great way to learn about multi-processing and concurrency. To establish a connection between the server and it's clients, the server listens on a TCP port on the local host and the clients dial on that port. After the connection is made, the client sends a json object to the server that contains the user names and the message. But before that json is sent, the name and message are both encrypted, while this is not apparent by using the client, you can see the effect on the server below.  

![Server image](./GoChat-Server.png)

As you can see, not even the server can see what the message includes, both the name and the message itself are AES encrypted. This means that if a malicious user were using a tool such as Wireshark, they would not be able to see the conversations.

