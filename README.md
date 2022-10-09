# NAME

SMS::Send::IN::NICSMS - Regional context SMS::Send driver to send messages via NIC's SMS Gateway ( https://smsgw.sms.gov.in )  

# VERSION

version 1.00   
 
# SYNOPSIS

    use SMS::Send;
    
    # Create a sender
    my $sender = SMS::Send->new(
      'IN::NICSMS',
      _login           => 'username',
      _password        => 'pin',
      _signature       => 'senderid',
      _dlt_entity_id   => 'dlt_entity_id',
    );
    # Send a message
    my $sent = $sender->send_sms(
        text => 'This is an example message',  # use actual DLT approved content template
        to   => '91XXXXXXXXX',                 # use actual 10 digit mobile number in place of 'XXXXXXXXXX'
        _dlt_template_id => 'dlt_template_id', # use the actual DLT template id for the text template above
    );
    if ($sent) {
    print "Message send OK\n";
    }
    else {
    print "Failed to send message\n";


# DESCRIPTION

An Indian regional context driver for SMS::Send to send SMS text messages via NIC's SMS Gateway in India - <https://smsgw.sms.gov.in> with 100% compliance to Telecom Regulatory Authority of India's (TRAI) TCCCPR 2018 norms which are accessible at <https://trai.gov.in/sites/default/files/RegulationUcc19072018.pdf>   

This is not intended to be used directly, but instead called by SMS::Send (see synopsis above for a basic illustration, and see SMS::Send's documentation for further information).   

The driver uses the NIC's HTTPS API mechanism for SMS.  This is documented in the "HTTPS/XML API Hand Book" for official customers/users from Govt of India, State governments, Union territories administrations, districts and other Government bodies of the service.   

# METHODS

## new

Constructor, takes argument pairs passed by SMS::Send, returns an SMS::Send::IN::NICSMS object.  See usage synopsis for example, and see SMS::Send documentation for further info on using SMS::Send drivers.   

**Mandatory arguments include:-**   

- _login

The username allotted to the user institution by NIC for use of the SMS service   

- _password

The PIN aka password for the username mentioned above   

- _signature

This is the 6 character (alphabet only) SenderID aka Header in DLT terminology   

- _dlt_entity_id

A 19 digit unique id assigned to the user institution while registering as PE (principal entity) on the DLT platform.   

**Additional arguments that may be passed include:-**   

- _endpoint

The HTTPS API endpoint. Defaults to C<https://smsgw.sms.gov.in/failsafe/HttpLink>  

- _debug

Whether debugging information is output or not.   



## send\_sms

Send the message - see SMS::Send for details. It requires three parameters to function with the NIC SMS gateway:   


- "text"

The DLT approved service implicit content template. The driver restricts it to 160 characters which forms the message body.   

- "to"

Destination mobile phone number in India. Numbered as per NNP 2003 i.e. 91XXYYYZZZZZ.   

- "_dlt_entity_id"

A 19-digit unique ID assigned to the B<text> content template above by DLT operator.   


# MISCELLANEOUS

NIC's implementation of DLT compliance requires a DLT approved content template along with its specific DLT Template ID to be passed to the HTTPS API call. While it is simple to implement that functionality in the Send Driver module, as of 2022-10-08 it is not possible to dieectly pass this DLT_TEMPLATE_ID to the driver from Koha ILS for which this driver is primarily written. To accomodate Koha's current limitation in this regard, a workaround is to embed the approved 19-digit DLT_TEMPLATE_ID at the beginning of each SMS message template in Koha.    


## Recipient phone number checks

Additional checks have been placed into the code for ensuring compliance with Indian National Numbering Plan 2003 (and its subsequent amendments). This measure is expected to prevent user generated errors due to improperly formatted or invalid mobile numbers, as noted below:    


- Example 1 : "819XXXXYYYYY" 

81 is an invalid country code. As an India specific driver, the country code must be 91.    

- Example 2 : "9XXXXYYYYY"

- Example 3 : "8XXXXYYYYY"

- Example 4 : "7XXXXYYYYY"

- Example 5 : "6XXXXYYYYY"

As per National Numbering Plan 2003, cell phone numbers (GSM, CDMA, 4G, LTE) have to start with 9XXXX / 8XXXX / 7XXXX / 6XXXX series (access code + operator identifier). A phone number that does not fit this template will be rejected by the driver.    

- Example 6 : "12345678"

- Example 7 : "12345678901234"

A phone number that is less than 10-digits long or over 12-digits long (including country code prefix) will be rejected as invalid input as per NNP 2003.     

- Example 8 : "+91 9XXXX YYYYY"

- Example 9 : "+91-9XXXX-YYYYY"

- Example 10 : "+919XXXXYYYYY"

- Example 11 : "09XXXXYYYYY"

Phone numbers formatted as above, when input to the driver will be handled as "919XXXXYYYYY" by the driver.    


## Error Codes

The following error code are returned by the NIC HTTPS API:

-  -2 : Invalid credentials.

- -19 : Unauthorised access.

- 000 : SMS Platform Accepted


_In the driver we only check if the error code is B<000> in the response sent back by the SMS Gateway._   


# INSTALLATION

See <https://perldoc.perl.org/perlmodinstall> for information and options on installing Perl modules.   


# BUGS AND LIMITATIONS

You can make new bug reports, and view existing ones, through the web interface at <http://rt.cpan.org/Public/Dist/Display.html?Name=SMS-Send-IN-NICSMS>.    

# AVAILABILITY

The project homepage is <https://metacpan.org/release/SMS-Send-IN-NICSMS>.   

The latest version of this module is available from the Comprehensive PerlArchive Network (CPAN). Visit <http://www.perl.com/CPAN/> to find a CPAN site near you, or see <https://metacpan.org/module/SMS::Send::IN::NICSMS/>.   

Alternatively, you can also visit the GitHub repository for this driver at <https://github.com/l2c2technologies/sms-send-in-nicsms>


# ACKNOWLEDGEMENT

State Central Library, Thiruvananthapuram, Kerala who approached regarding the possibility of development of a DLT compliant SMS Send driver in Perl for NIC's SMS Gateway service and provided the necessary inputs to develop and test the driver. Thanks also to National Informatics Centre, MeitY, Govt of India for the developer documentation of their API for SMS service. Developer colleagues from the Koha Community and various CPAN contributors who's prior work on SMS::Send regional drivers acted as sources of inspiration for the code.   


# AUTHOR

Indranil Das Gupta <indradg@l2c2.co.in> (on behalf of L2C2 Technologies).   


# COPYRIGHT AND LICENSE

Copyright (C) 2022 L2C2 Technologies   

This is free software; you can redistribute it and/or modify it under the same terms as the Perl 5 programming language system itself, or at your option, any later version of Perl 5 you may have available.   

This software comes with no warranty of any kind, including but not limited to the implied warranty of merchantability.   

Your use of this software may result in charges against / use of available credits on your NIC SMS Service account. Please use this software carefully keeping a close eye on your usage and/or billing, The author takes no responsibility for any such charges accrued.    

Document published by L2C2 Technologies [ https://www.l2c2.co.in ]
