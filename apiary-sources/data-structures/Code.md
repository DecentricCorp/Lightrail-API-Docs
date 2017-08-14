## Code (object)
+ currency (string, required) - {{currency}}
+ initialValue (number, required) - The value of the code at creation in the smallest currency unit 
  (such as cents).
+ programId (string, required) - The id of the Program for which the code will be created from.
+ expires (string, optional) - The code's expiry date and time.  This field will be ommitted if the 
  code was not created with an expiry.  ISO-8601 format.
+ startDate (string, optional) - The code's start date and time.  This field will be ommitted if 
  the code was not created with an start date.  ISO-8601 format.
+ metadata (object) - A collection of key/value pairs of additional information about the code. 
  The `giftbit_*` namespace for keys is reserved. 
