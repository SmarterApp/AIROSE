The data in both training and validation folders need to be the same format.
Currently both folders have placeholder sample files. Place your 
training set - each in a separate file - in the training folder and 
place your validation set - each in a separate file - in the validation 
folder.

The sample files have the format:

<uniqueid>,<score 1>,<score 2>,<domain score>,<response>


Each file will just be that one line but containing actual values for 
"uniqueid", "score 1", "score 2", "domain score" and "response". There 
should not be a header.
