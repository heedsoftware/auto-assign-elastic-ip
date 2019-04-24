# Auto Assign Elastic IP
Useful script to automatically assign an Elastic IP to an EC2 instance on start up from a pool of IPs. The script works well if you are starting your instances using AWS Autoscaling but it can also be used if you are starting them manually.

## Setup
First, you will need to allocate a pool of Elastic IPs, you can find out more information about how to allocate IP addresses [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html). Once you have allocated your address you will need to add a tag named Environment to each of them, where the value is the name of your pool, for example 'Production'.

You will need to have at minimum the same number of IP addresses allocated as you will have instances, but if possible we recommend having at least one additional spare.

Once you have setup your IP pool you can now start up your instances. The auto-assign-elastic-ip script will need to be start on instance start up. One simple way of achieving this would be to download the script from an S3 bucket using the instance's User data, for example:

~~~~
#!/bin/bash

aws s3 cp --region eu-west-1 s3://ec2-scripts.heed.io/auto-assign-elastic-ip.sh ~/auto-assign-elastic-ip.sh

chmod +rx ~/auto-assign-elastic-ip.sh

~/auto-assign-elastic-ip.sh
~~~~

Alternatively, you could set your script to be executed on start up and save an updated AMI.

The instances will need to be started with a matching Environment tag as your IP pool, otherwise it will fail to find any addresses to allocate. In our example above, you would need to add the tag Environment=Production.

Once everything is setup, your instances will have an Elastic IP automatically assigned on start up.

## IAM Permissions
The following permissions have been identified as the minimum required that your IAM Role for your instance will require to execute the script.
~~~~
{
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:AssociateAddress",
        "ec2:DescribeAddresses",
        "ec2:DescribeTags",
        "ec2:DescribeInstances"
      ],
      "Resource": "*"
    }
  ]
}
~~~~
If you are downloading your script from S3 as described above, then additional permissions will be required over your S3 bucket.
## License
~~~~
MIT License

Copyright (c) 2019 Heed Software Ltd

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
~~~~
