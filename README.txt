CIS192 Final Project
Zixuan Zhang and James Wang
12/12/2016

————————————————————————————————
Contents
————————————————————————————————

* Project Overview
* Required modules
* Methodology and Files
* Project Requirements

————————————————————————————————
Project Overview
————————————————————————————————

The goal for our project was to train up two recurrent neural networks on debate and campaign speech text from Hillary Clinton and Donald Trump from the 2016 election cycle. We would then generate text from each of the models by using output from the other model. Each candidate had over 100,000 3-gram training examples.

————————————————————————————————
Required modules and environment
————————————————————————————————
We built our RNNs using the neural net library Keras and trained them on EC2 virtual server instances provided by Amazon Web Services.

Here are the instructions for setting up your EC2 virtual server to run Keras:

- Choose an instance: m4.16xlarge (64 CPUs, 257 GB RAM, Instance Storage: EBS only). Others recommend to use g2 or p2 instances (for GPU computing)
- Upgrade memory to > 16 GB.
- Create key pair
- Security settings: change to “My IP”
- Launch instance
- Install bz2: sudo yum install  bzip2-devel
- Install Python3
- Upgrade pip: python3 -m pip install --upgrade pip
- Install numpy: sudo pip3 install numpy
- Install atlas-devel, lapack, scipy
- Install keras:  sudo pip3 install keras
- Install tensorflow:
>- export TF_BINARY_URL=https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.12.0rc0-cp34-cp34m-linux_x86_64.whl
>- sudo pip3 install --upgrade $TF_BINARY_URL
- Install bs4: sudo pip3 install bs4
- Install requests: sudo pip3 install requests
- Install gensim: sudo pip3 install gensim
- Install html5lib: sudo pip3 install html5lib
- Install h5py: sudo pip3 install h5py
- Copy files from local directory into AWS instance: scp -i gpu-keras.pem rnn.py create_word2vec.py ec2-user@ec2-54-89-149-120.compute-1.amazonaws.com:~/.
- run files within EC2 server

————————————————————————————————
Methodology and Files
————————————————————————————————

(1) Go to “create_word2vec.py”

(2) Scrape debate transcripts and speech transcripts from: http://www.presidency.ucsb.edu/
- see the “scraper” method in “create_word2vec.py” file within the “Word2Vec” class

(3) Create vector representations of the words with word2vec algorithm
- see “__call__” magic method in “create_word2vec.py” file within the “Word2Vec” class

(4) Generate 3-grams of vector representations and their corresponding word labels (this will serve as the input to the RNNs)
- see “generateInputData” method in “create_word2vec.py” file within the “Word2Vec” class

(5) Go to “rnn_clinton.py” or “rnn_trump.py” files

(6) Create word2vec dictionaries by initializing a “Word2Vec” instance called “preprocessing”, and calling the instance using the “__call__” magic method

(7) Load training data and labels with “generateInputData” method

(8) Train data using RNNs (specifically, Long Short Term Memory networks, which are a special kind of RNN. LSTMs can connect more distant information from the past (long-term dependencies) to the present better than regular RNNs can.)
- we trained two LSTMs on both Clinton and Trump’s text data. Each LSTM had 1 layer of 1200 units and a batch size of 32 training instances. We also set the dropout rate to 0.3, to prevent overfitting. We trained the Clinton model with 50 epochs and the Trump model with 100 epochs. This way we could compare the performance of the LSTMs when given more epochs to train.

(9) After training up both models within AWS, we save the output files containing the weights of the LSTMs (naming scheme is "weights-improvement-..."). By running “python3 clinton_rnn.py” or “python3 trump_rnn.py” in your local Terminal, this will load the weights and prompt the user to input 3 words to have the Clinton model or Trump model complete the sentence.

(10) We have built in some error messages to catch bad user input, however if the model were to crash, it would simply need to be restarted.

————————————————————————————————
Project Requirements
————————————————————————————————

- Define and use a custom class, and define and use at least one magic method
x custom class is “Word2Vec”
x magic method is “__call__”

- Use at least 3 modules we talked about in class
x requests, bs4, re, numpy

- Use at least on custom decorator or generator
x “lstm” function in “rnn_clinton.py” and “rnn_trump.py” files utilizes a generator to iterate over user input and generate text from the Clinton or Trump models

- Documentation
x README
x Explain how to use the models
x Indicate how requirements were met
x Names of all group members
