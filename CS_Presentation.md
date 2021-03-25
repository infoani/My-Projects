# Introdution:

Hello everyone, I am Anirban with my team-mates Praveen and Irfan. We're from Object Storage development team and today we'll be presenting the "Enhanced" PreAuthenticatedRequests.  

# Slide 1 (What is a PAR)

## Before starting with the presentation let me give a brief introduction of Pre-Authenticated requests.
1. Pre-Authenticated Reqeusts are a mechanism to allow access to resources on Object-Storage to someone without sharing the credentials, as long as the one who's creating it has access to it.

2. When you create a pre-auth request you get an unique url back.
3. Using which anyone can access objects from object storage with simple tools like curl, wget. The url itself acts like credentials.

# Slide 2 (Why enhance)

## So, let's talk about why there is a need to enhance the existing PARs:

### Customers have been coming back to us with the request for supporting 
1. "Read-Operation" on the bucket, the present system only allows "Writes" to a bucket.
2. They also wanted support for __"object-with-prefixes"__, as this would help them restrict what a PAR can do on a bucket.
3. An finally __listing-of-objects__ using PARs.

### And we wanted to Improve PARs because
1. This feature would be replacing public buckets. Public buckets as we know are not secure as they don't use IDENTITY and don't have support for IAM policees, which makes them a big security threat to our systems.

# Slide 3 (Customer requested features)

## Continuing more into the feature:
### We have now support for Read Any Object,
1. which enables PARs to read any object from the bucket. Currently UPLOAD is allowed on both object and bucket level but READ-ing objects is only possible at the object level. 
2. To put this into context, if a customer wants to download 1000 objects via PARs they'll have to create 1000 PARs, one for each object.
3. Having READ operation enabled for buckets would make this much simpler. They would be able to READ many objects with a single PAR.

### Customers requested that we make support for "Objects-With-Prefixes" available for them. 
1. So that any operation performed through the PARs is scoped to the prefix. 
2. This would really expand their usage of PARs in multitenant situations. For example, if customer has multiple tenancies, they could configure __PARs-with-prefixes__ in such a way that each untrusted host is only able to upload log files to that specific host-prefix and not anywhere else.

# Slide 4 (PAR features required to remove public buckets)

## Public buckets do have a lot of security drawbacks, so we'd like to replace them with PARs.

### To enable this migration we would need support for
1. Reading any object in the bucket
2. And Listing objects in the bucket. So if we perform a GET operation on a PAR without providing an object name it should not fail, it should list the objects in the bucket instead.

# Slide 5 (Public buckets vs PARs)
## Let us see a comparison between PARs and public buckets.

1. Public buckets bypass the IAM, so any IAM feature like limiting access by IP, time-of-day or object name can not be applied to it. PARs on the other hand assumes the identity of the user who created it, so every request from PAR goes through authorization.
2. Public bucket url is easy to guess where PARs use a 384bit random string in the uri which is very hard to guess.
3. Public buckets never expire where as every PAR has an expiration time.
4. PARs can be disabled any time where as Revoking public bucket access without compromising the url would mean that we have to copy all the data to a new bucket.

# Slide 6 (Customer Exp)

## We currently have 4 types of PAR ObjectRead, ObjectWrite, ObjectReadWrite and AnyObjectWrite. 
- Customers would be seeing 2 new types AnyObjectRead and AnyObjectReadWrite which would be used to support READ Any on buckets.

## We allow a prefix to be specified when creating a PAR of type AnyObject{Read,Write,ReadWrite}. The following rules apply when we talk about prefixes.
1. The object name on the PAR will be treated as a required prefix
2. Any object accessed through PAR must have a name that starts with the prefix.
3. The prefix is not automatically added to object names, it must be specified by the caller.

# Slide 7 (Customer Exp contd..)

## We have a new option that allows listing on a Read Any PAR by doing a GET operation.
1. Listing only works with "READ_TYPE" PARs such as AnyObjectRead or AnyObjectReadWrite and we have the option to enable or disable it at the time of creation. It is disabled by default.
2. If the PAR has a prefix it will be auto applied while listing. Requester can also choose to pass a prefix in the parameter along with other parameters like limit, start, end etc. The parameters that apply here are the same as V2 ListObjects API.

# Slide 8 (Scope Slide)

## Scope of the project
1. We wanted to enable Read Any on bucket PARs, So GET and PUT operations are supported in both objects and buckets.
2. DELETE is not supported yet.
3. PARs now support prefixes.
4. PARs cannot be modified once created, so listing can not be enabled on existing PARs, we have to create new ones.
5. We don't require any new permissions while creating READ-Any PARs, existing PAR_MANAGE should be sufficient.

# Slide 9 (API Slide)

### As for the API changes 

1. we're adding two new accesstypes "AnyObjectRead" and "AnyObjectReadWrite". 
2. We're also adding a new property "BucketListingAction" which would be used to enable listing on a bucket. It takes two values "Deny" and "ListObjets", "Deny" being the default.  

# Slide 10 (Common Issues Slide)

## I have listed down some common issues which customers might face during the adaptation phase of the enhancement.

1. Customer might be seeing a 401 in-case they try to list or retrieve objects with incorrect prefix. The solution in this case is to look for the correct prefix. Another way to figfure out the prefix would be to try and list the objects using the PAR, only if listing is enabled.

2. During the adaptation they might come up with a 404 for trying the read an object using Write Any PAR, which was the only option so far. Since the existing PARs can not be updated to add Read capabilities, we would need to create a new PAR with the desired confirguration.

3. Customer might also get a 404 while trying to list objets while listing action property is set to Deny. In this case also we need to create a new PAR with listing enabled.

# Slide 11 (Demo Slide)

Now we'll do a short demo to present the new PAR features using the console. I will let Irfan take over now for the demo.
