_scroll and skip down for music_

## What is this?
Another neural network that generates music.  While the output does not generally sound “like” the song that was fed to the network, each input song tends to produce its own ambient signature.  The network is therefore both songeater and SONGSHTR.

## AKA...
An LSTM+VAE neural network implemented in Keras that trains on raw audio (wav) files and can be used to generate new wav files. Like [char-rnn](https://github.com/karpathy/char-rnn) for music.

## Hasn't this been done before?
Yes.  [Karpathy](http://karpathy.github.io/2015/05/21/rnn-effectiveness/) came first.  

My model feeds on raw audio (as opposed to MIDI files or musical notation)… so [GRUV](https://github.com/MattVitelli/GRUV) would be the closest comparison.  Since GRUV was written in Python/Keras, I was able to borrow liberally from the code, especially the processing and backend stuff (although I never actually got the program to run... py 2 v 3 issues I believe).  Thank you to Matt & Aran.

[This](http://www.asimovinstitute.org/analyzing-deep-learning-tools-music/) is a summary of other things that are out there.

## So what's new?
Two major changes to the char-rnn/GRUV type models:
- Add a VAE to a Karpathy-style char-rnn: char-rnn succeeds in creating interesting text by changing the output’s “temperature” during the generation phase.  This is possible because the character-set of any text is finite/small/one-hot coded.  Given some sequence of seed characters, char-rnn predicts the next character in the sequence by outputting a probability weight for _every possibly character_ in the character-set.  Char-rnn can avoid picking only the most likely prediction, which would lead to it spitting out banal and repetitive text.  The char-rnn approach does not work with raw audio since our wav input/outputs are real numbers that cannot be one-hot coded (well, they _can_… but not elegantly).  A vanilla LSTM model trained on real numbers would generate only one real number as an output, not a distribution of likely real outputs.  So a [Variational Auto-Encoder](https://blog.keras.io/building-autoencoders-in-keras.html) is tacked on to the base LSTM architecture… and otherwise the model is set up to work very much like char-rnn.  The addition of the VAE makes a marked difference to the repetitiveness of the generated music.
- DCT domain in addition to FFT: The DCT domain generates songs within a narrower frequency range and adds coherence/clarity.  FFT models capture the full frequency spectrum and have a more ethereal quality.

In addition, there was a lot of hyper-parameter tweaking.  Since the aim of this exercise was to create aesthetically pleasing sounds, I couldn't really test by quantitatively (eg. validation loss or perplexity).  All tweaking subjective.
- I mostly worked on 3-10 minute songs.  Backdrifts, the 9th, Harvest Moon, Johnny-Be-Goode, etc.  Songs that had been high-pass filtered above 500hz tended to produce more interesting samples.
- Single to 3 layered LSTM models.  128 to 1024 neurons per layer. More neurons/layers produced results that were _different_ not _worse_.
- The single-layer, 128 neuron models could be trained on my non-GPU-possessing laptop in about 30 minutes.  I found that generative quality peaked around 5 epochs (2-3000 iterations).  Some models really tailed off after that while others held on for 15+ epochs.  The larger 1024-neuron models were trained on an AWS-p2 instance and took about an hour to get through 5 epochs.   Again, training beyond 5 epochs brought marginal changes at best.  Training benefits clearly peter out too soon and most models become stuck... this would have been a more a troubling sign in a more traditional rnn use-case.
- Once a model is trained up, it can be used to generate songs fairly quickly, even on my laptop a 3 minute song took 5-7 minutes to plop out.
- The produced songs DID NOT sound like what was fed into them.  This program does NOT “remix” songs – ok not always, sometimes Radiohead came out as something that sounded something like Radiohead.
- I post-processed in Audacity.  Normalization, click-removal, equalization, low/high-pass filters, reverbs, stacking up multiple generations of songs produced by a model, etc.  What I did not do was add soundclips from any source other than the wav files given to me by SONGSHTR.

## All right, show us what we've got here...
Ambient is one description of it.  Which is another way of saying the compositions are slow-building - best if you have some time to just let them be.  Brian Eno, this is not.  Also headphones recommended.

<iframe width="100%" height="450" scrolling="no" frameborder="no" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/playlists/317091057&amp;color=ff5500&amp;auto_play=false&amp;hide_related=false&amp;show_comments=true&amp;show_user=true&amp;show_reposts=false"></iframe>
