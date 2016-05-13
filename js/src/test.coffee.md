
# Mnist Test

    mnist = require 'mnist'
    synaptic = require 'synaptic'

Creates a training set of 700 images and a testSet with 20 elmts.
When creating the sets manually, it is important to make sure there are no duplicate 
elements in the sets.
If you’re using the MNIST digits library, this is checked automatically.

    console.log '1. Creating set from MNIST...'
    set = mnist.set 700, 20

After creating the data for training and testing, we can set up the network. 
We will use the library synaptic.js, which gives us the possibility to create 
a neural network and configure various parameters. 
First of all, we have to determine how many input and output neurons are needed.
As the size of each image is 28x28px, the number of pixels the network has to 
take as input is 28 x 28 = 784. The digits should be assigned to one of ten classes,
so the number of output neurons will be 10.
Furthermore, the network should have at least one hidden layer, which in this 
example is set to consist of 100 neurons. After creating the data for training 
and testing, we can set up the network.
We will use the library synaptic.js, which gives us the possibility to create
a neural network and configure various parameters. First of all, we have to 
determine how many input and output neurons are needed. As the size of each image
is 28x28px, the number of pixels the network has to take as input is 28 x 28 = 784.
The digits should be assigned to one of ten classes, so the number of output neurons will be 10. 
Furthermore, the network should have _at least_ one hidden layer, which in this 
example is set to consist of 100 neurons.

    #const Network = synaptic.Network;
    #const Trainer = synaptic.Trainer;

    inputLayer = new synaptic.Layer(784);
    hiddenLayer = new synaptic.Layer(100);
    outputLayer = new synaptic.Layer(10);

    inputLayer.project hiddenLayer
    hiddenLayer.project outputLayer

    network = new synaptic.Network
        input: inputLayer
        hidden: [hiddenLayer]
        output: outputLayer

To train the network with our training set, we can use the Trainer provided by synaptic.js.
The train() function takes the data used for the training and a list of parameters
for the configuration of the trainer.

As options, you can set the 'rate', which is the learning rate for the training.
The 'iterations' define, after how many iterations the training determines. 
The 'error' is the minimum error that can be reached during the training,
if it is achieved, the training stops. By setting the ‘shuffle’ option, you can
specify if the training set is ordered randomly or not. You can find more detailed
information about all possible options in the synaptic.js documentation.

To get the general idea, in this example we set the maximum number of iterations
to 20 to make sure we don’t have to wait for hours before the training is finished.
Note that the network won’t be trained very well after only 20 iterations.
To get better results, you’ll have to increase the number and be patient. 

If you want to see the progress of training, you can use the 'log' option to print
out the current error of the network.
The lower the error, the better the network is trained. 

You might notice that the error fluctuates a bit instead of shrinking constantly,
but on the whole it gets smaller. If this is not the case, you can try working
with a smaller learning rate. 

    console.log '2. Creating trainer...'
    trainer = new synaptic.Trainer network
    console.log '3. Training from set...'
    trainer.train set.training,
      rate: .2,
      iterations: 20,
      error: .1,
      shuffle: true,
      log: 1,
      cost: synaptic.Trainer.cost.CROSS_ENTROPY

After the training is finished, the testset can be used to check how good the
classification by the network works. For this, we can use the activate() function
of the network, which takes the element to classify as parameter.
To check the result, it can be printed out and compared to the expected output.
Correct output is a vector of dimension 10, each cell is a boolean. 1 means that the image is a
graphical representation of the index number.

Ex:

1,0,0,0,0,0,0,0,0,0 = 0
0,1,0,0,0,0,0,0,0,0 = 1
0,0,1,0,0,0,0,0,0,0 = 2
...

    console.log '4. Comparing...'
    for k, i in set.test
      calcvalue = network.activate k.input
      console.log "[#{i}]\t #{k.output}\t#{calcvalue}"
