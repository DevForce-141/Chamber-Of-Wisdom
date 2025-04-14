While Adding DNS Records, 69.420% chances are that you've copied the text and will paste into the records manager
fields. It's important to consider that if anything additional is being added to the text; Like if you pasted
the record into browser url field and then copied again from there, it would contain protocol ``http`` or ``https``.

Depending on record type, your record manager may complain about the record not being valid. For a longer record
like AWS Application Load Balancers (ALB) CNAME records, it might not be obvious as they are long and would likely
be longer than the length of input field, making the start of the record out of sight.

Another mistake is to have domain name inside record name. Some Record managers will inform you about this and some may not.
GoDaddy sometimes doesn't inform about this. As a result, you end with an incorrect record where your domain name is duplicated.

For example, You meant to add "test.example.com" as the record name but you ended up with "test.example.com.example.com" as the record name
because of including ``example.com`` in the record name field.

Keep your eyes on steroids OR High alert while working with DNS records or you'll lose your sleeping hours!

