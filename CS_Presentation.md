Good morning/evening everyone. I am anirban from oss and I will be presenting the latest PAR enhancements followed by a short demo. 

Slide 1 (What is a PAR)

## Before starting with the presentation let me give a brief intro of Pre-Authenticated requests.
1. Pre-Authenticated Reqeusts are a mechanism to securely provide access to resources on Object-Storage without sharing the credentials.
2. Transaction using PAR can be performed using simple tools like curl, wget or even browser. 
3. PAR assumes the identify of the creator and keeps the user oblivious from all the security related aspects while being very flexible in terms of usage. 
Ex: You could add headers and parameters to the PAR url and expect different behaviour all while maintaining the security.

# Slide 2 (Why enhance)

## so We have made the following enhancements to PAR based on our customer feedback:

1. To start with, There had been a lot of requests from customers to support  "Read-Operation" on the bucket. 
For long we had only been supporting writes to the bucket. Problem with this scenario is that a customer has to create 100 PARs if they wanted to share 100 objects in a bucket.
Now this is not only impractical but also requires additional effors in maintaining the PARs in terms of security. With this feature we move away from that impracticality.

2. Additionally, the customers wanted us to support pars with prefixes, which would restrict a PARs access only to certain prefixes. This is useful in scenarios 
where they would want to give access only to a family of objets without giving away aceess to the full bucket. So this provides more granular control over what a PAR can do.

3. And finally, we now provide the option to enable listing usina a PAR. Enabling this would let customers see all the contents of a bucket or just the ones restricted by a prefix.


## We as a service wanted to move our customers away from using public buckets. Because public buckets have a lot of security drawbacks.
Like the following:
1. They would bypass IAM policies and IDENTITY altogether, whereas PAR assumes the IDENTITY of the creator so we can limit access by IP addresses, 
time of the day or even object name.
2. PAR contains a 384 bit random string which is very hard to guess or tamper with which adds on to more security.
3. PARs have an expiration date while public buckets don't, makes it easier to manage PARs.
4. Furthermore, PAR can be disabled anytime if we percieve a security threat where as public buckets don't allow us such flexibility.

So, these were a few points explaining the enhanced features and the reason behind the move. I will now get on with a short demo to further illustrate the features.
