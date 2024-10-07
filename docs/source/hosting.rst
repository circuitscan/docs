Hosting your own Circuitscan instance
=====================================

.. note::

   This guide assumes knowledge of AWS and CloudFormation. You should have already cloned the `main circuitscan repository <https://github.com/circuitscan/circuitscan>`_ to your local machine.

Circuitscan repositories provide all the necessary Cloudformation templates and scripts for hosting your own independent instance.

1. Instantiate ``frontend-stack.yaml``. This will create the S3 buckets configured for hosting a website and a redirect bucket with a ``www.`` prefix.

    The official Circuitscan instance uses CloudFlare in front of S3 for its automatic HTTPS certificates.

2. Instantiate ``artifacts-stack.yaml``. This will create an S3 bucket with public website hosting and CORS configuration so that artifacts can be loaded from other domains.

    This bucket is accessed directly through its ``<bucket-name>.s3.<region>.amazonaws.com`` domain because CloudFlare does not support range headers (used to load individual files from the build artifact zips) on large files. AWS CloudFront could be used to provide better download speeds across multiple regions.

3. Create an AWS ECR repo for the main verification server Docker images. Update the ``deploy:server`` NPM script in the ``package.json`` of your local clone of the main ``circuitscan`` repository to correspond to your AWS CLI configuration and account number. Finally, run both ``yarn build:server`` and ``yarn deploy:server`` to push the Docker image of the verification server to your new ECR repository.

4. Instantiate ``server-stack.yaml``, pointing at your newly created repository. This will create the AWS Lambda function and output its invocation URL.

    .. note::

       The RecaptchaSecretKey parameter can be left blank to disable Google Recaptcha verification for API Key generation

5. Configure a verified sender in your AWS SES console so that instance alerts can be sent out.

6. Upload an SSH public key to your AWS EC2 console so that you can access running instances to debug if necessary.

7. Instantiate ``instance-service.yaml``, pointing the ``CloneUrls`` parameter at the repositories you wish to allow as compilation pipelines. For each invocation, this repo will be cloned in the new EC2 instance performing the compilation. See the `Circuitscan pipeline runner <https://github.com/circuitscan/circuitscan-pipeline-runner>`_ for more information.

8. Update ``public/cli.json`` with the URLs output by your CloudFormation stacks.

9. Update the ``deploy:frontend`` NPM script with your frontend S3 bucket name created in step 1 and invoke the script with ``yarn deploy:frontend``.

Congratulations! You now have a complete installation of Circuitscan on your own AWS account.
