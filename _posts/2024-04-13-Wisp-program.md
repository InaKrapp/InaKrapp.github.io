---
title: "Wisp: A locally running version of Whisper"
layout: post
---
Whisper is a transcription software that allows to turn audio files into text. I created a
 locally running version of it, Wisp, with the aim to give it a simple, intutive user interface. My project can be found here: [Wisp](https://github.com/InaKrapp/whisper-locally-running)

Whisper itself has been developed by OpenAI, which is also the company behind ChatGPT and several other Artificial Intelligence programs. 
You can try it out in one of the spaces here: ‚[Whisper Demo](https://huggingface.co/openai/whisper-large-v3)‘.
Unlike ChatGPT, Whisper does not have a user interface designed by OpenAI. Its demos, as you might have seen if you tried it out above, often are used by many people at the same time. Since they all send their requests to the same computer, people may have to wait a very long time before they receive the text. Alternatively, Whisper can be run locally, using Python, but for anyone who does not know how to use a programming language, this is not an option.

So the aim of my project was to make Whisper easy to use for anyone, on their own computer.

 Wisp is supposed to run without internet connection. Any user runs it on their computer, meaning that the users won‘t have to wait before the program finished the text of someone else. Since it has a graphical user interface, it is easy to use for anyone who is familiar with standard office software like Microsoft Office. 

Like with many of my other projects, I learnt a lot in the process of building this program. Before I started, I had no experience in working with audio data and very little with building a graphical user interface in Python. It was also my first time I turned a Python program into an executable windows program.
