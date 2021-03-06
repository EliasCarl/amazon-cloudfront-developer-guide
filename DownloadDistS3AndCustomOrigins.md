# Using Amazon S3 Origins and Custom Origins for Web Distributions<a name="DownloadDistS3AndCustomOrigins"></a>

When you create a web distribution, you specify where CloudFront sends requests for the files\. CloudFront supports using several AWS resources as origins\. For example, you can specify an Amazon S3 buckets or an AWS Elemental MediaStore container, as well as custom origins, such as an Amazon EC2 instance or your own HTTP web server\.

**Topics**
+ [Using Amazon S3 Buckets for Your Origin](#concept_S3Origin)
+ [Using Amazon S3 Buckets Configured as Website Endpoints for Your Origin](#concept_S3Origin_website)
+ [Using an AWS Elemental MediaStore container or an AWS Elemental MediaPackage Channel for Your Origin](#concept_AWS_Media)
+ [Using Amazon EC2 or Other Custom Origins](#concept_CustomOrigin)

## Using Amazon S3 Buckets for Your Origin<a name="concept_S3Origin"></a>

When you use Amazon S3 as an origin for your distribution, you place any objects that you want CloudFront to deliver in an Amazon S3 bucket\. You can use any method that is supported by Amazon S3 to get your objects into Amazon S3, for example, the Amazon S3 console or API, or a third\-party tool\. You can create a hierarchy in your bucket to store the objects, just as you would with any other Amazon S3 bucket\.

Using an existing Amazon S3 bucket as your CloudFront origin server doesn't change the bucket in any way; you can still use it as you normally would to store and access Amazon S3 objects at the standard Amazon S3 price\. You incur regular Amazon S3 charges for storing the objects in the bucket\. For more information about the charges to use CloudFront, see [CloudFront Reports](reports.md)\.

**Important**  
For your bucket to work with CloudFront, the name must conform to DNS naming requirements\. For more information, go to [Bucket Restrictions and Limitations](http://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html) in the *Amazon Simple Storage Service Developer Guide*\.

When you specify the Amazon S3 bucket that you want CloudFront to get objects from, in general, use the following format:

`bucket-name.s3.amazonaws.com`

If your bucket is in the US Standard region and you want Amazon S3 to route requests to a facility in Northern Virginia, use the following format:

`bucket-name.s3-external-1.amazonaws.com` 

When you specify the bucket name in this format, you can use the following CloudFront features:
+ Configure CloudFront to communicate with your Amazon S3 bucket using SSL\. For more information, see [Using HTTPS with CloudFront](using-https.md)\.
+ Use an origin access identity to require that your users access your content using CloudFront URLs, not by using Amazon S3 URLs\. For more information, see [Using an Origin Access Identity to Restrict Access to Your Amazon S3 Content](private-content-restricting-access-to-s3.md)\.
+ Update the content of your bucket by submitting `POST` and `PUT` requests to CloudFront\. For more information, see [HTTP Methods](RequestAndResponseBehaviorS3Origin.md#RequestS3HTTPMethods) in the topic [How CloudFront Processes and Forwards Requests to Your Amazon S3 Origin Server](RequestAndResponseBehaviorS3Origin.md#RequestBehaviorS3Origin)\.

Do not specify the bucket using the following formats:
+ The Amazon S3 path style, `s3.amazonaws.com/bucket-name`
+ The Amazon S3 CNAME, if any

## Using Amazon S3 Buckets Configured as Website Endpoints for Your Origin<a name="concept_S3Origin_website"></a>

You can set up an Amazon S3 bucket that is configured as a website endpoint as custom origin with CloudFront\.
+ When you configure your CloudFront distribution, for the origin, enter the Amazon S3 static website hosting endpoint for your bucket\. This value appears in the Amazon S3 console, on the **Properties** page under **Static Website Hosting**\. For example:

  `http://bucket-name.s3-website-us-west-2.amazonaws.com`

For more information about specifying Amazon S3 static website endpoints, see [Website Endpoints](http://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteEndpoints.html) in the Amazon S3 documentation\.

When you specify the bucket name in this format as your origin, you can use Amazon S3 redirects and Amazon S3 custom error documents\. For more information about Amazon S3 features, see the [Amazon S3 documentation](http://docs.aws.amazon.com/AmazonS3/latest/dev/Welcome.html)\. \(CloudFront also provides custom error pages\. For more information, see [Customizing Error Responses](custom-error-pages.md)\.\) 

Using an Amazon S3 bucket as your CloudFront origin server doesn't change it in any way\. You can still use it as you normally would and you incur regular Amazon S3 charges\. For more information about the charges to use CloudFront, see [CloudFront Reports](reports.md)\.

**Note**  
If you use the CloudFront API to create your distribution with an Amazon S3 bucket that is configured as a website endpoint, you must configure it by using `CustomOriginConfig`, even though the website is hosted in an Amazon S3 bucket\. For more information about creating distributions by using the CloudFront API, see [CreateDistribution](http://docs.aws.amazon.com/cloudfront/latest/APIReference/API_CreateDistribution.html) in the *Amazon CloudFront API Reference*\.

## Using an AWS Elemental MediaStore container or an AWS Elemental MediaPackage Channel for Your Origin<a name="concept_AWS_Media"></a>

To stream on\-demand or live video in CloudFront, you can set up an Amazon S3 bucket that is configured as an AWS Elemental MediaStore container, or create a channel and endpoints with AWS Elemental MediaPackage\. Then you create and configure a distribution in CloudFront to stream the video\.

For more information and step\-by\-step instructions, see the following topics:
+ [Configuring On\-Demand Video with AWS Elemental MediaStore](on-demand-video.md#on-demand-streaming-mediastore)
+ [Serving Live Video Formatted with AWS Elemental MediaPackage](live-streaming.md#live-streaming-with-mediapackage)

## Using Amazon EC2 or Other Custom Origins<a name="concept_CustomOrigin"></a>

A custom origin is an HTTP server, for example, a web server\. The HTTP server can be an Amazon EC2 instance or an HTTP server that you manage privately\. An Amazon S3 origin configured as a website endpoint is also considered a custom origin\.

When you use a custom origin that is your own HTTP server, you specify the DNS name of the server, along with the HTTP and HTTPS ports and the protocol that you want CloudFront to use when fetching objects from your origin\.

Most CloudFront features are supported when you use a custom origin with the following exceptions:
+ **RTMP distributions**—Not supported\.
+ **Private content**—Although you can use a signed URL to distribute content from a custom origin, for CloudFront to access the custom origin, the origin must remain publicly accessible\. For more information, see [Serving Private Content through CloudFront](PrivateContent.md)\.

Follow these guidelines for using Amazon EC2 instances and other custom origins with CloudFront\. 
+ Host and serve the same content on all servers that are serving content for the same CloudFront origin\. For more information, see [Origin Settings](distribution-web-values-specify.md#DownloadDistValuesOrigin) in the [Values That You Specify When You Create or Update a Web Distribution](distribution-web-values-specify.md) topic\.
+ Log the `X-Amz-Cf-Id` header entries on all servers; CloudFront requires this information for debugging\.
+ Restrict access requests to the HTTP and HTTPS ports that your custom origin listens on\.
+ Synchronize the clocks of all servers in your implementation\. Note that CloudFront uses Coordinated Universal Time \(UTC\) for signed URLs and signed cookies, for access logs, and reports\. In addition, if you monitor CloudFront activity using CloudWatch metrics, note that CloudWatch also uses UTC\.
+ Use redundant servers to handle failures\.
+ For information about using a custom origin to serve private content, see [Using an HTTP Server for Private Content](PrivateContent.md#private-content-overview-choosing-origin)\.
+ For information about request and response behavior and about supported HTTP status codes, see [Request and Response Behavior](RequestAndResponseBehavior.md)\.

If you use Amazon Elastic Compute Cloud for your custom origins, we recommend that you do the following:

1. Use an Amazon Machine Image that automatically installs the software for a web server\. For more information, see the [Amazon EC2 documentation](http://aws.amazon.com/documentation/ec2/)\.

1. Use an Elastic Load Balancing load balancer to handle traffic across multiple Amazon EC2 instances and to isolate your application from changes to Amazon EC2 instances\. For example, if you use a load balancer, you can add and delete Amazon EC2 instances without changing your application\. For more information, see the [Elastic Load Balancing documentation](http://aws.amazon.com/documentation/elasticloadbalancing/)\.

1. When you create your CloudFront distribution, specify the URL of the load balancer for the domain name of your origin server\. For more information, see [Working with Web Distributions](distribution-web.md)\.