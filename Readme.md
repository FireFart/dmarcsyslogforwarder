# DMARCSYSLOGFORWARDER

This program is used to read in dmarc reports from a dedicated IMAP mailbox and converts them to single entries and sends them in XML format via syslog to a remote server. This can be used to feed dmarc reports into your favourite SIEM.

As each dmarc report can contain multiple entries the report is split into single reports. The source ip from the report is also resolved via DNS.

This program does not work on windows as the golang syslog library is not compatible.

## Syslog XML Format

```xml
<syslog_entry>
  <version></version>
  <domain>google.com</domain>
  <date_begin>1636416000</date_begin>
  <date_end>1636502399</date_end>
  <report_id></report_id>
  <org_name>google.com</org_name>
  <email>noreply-dmarc-support@google.com</email>
  <extra_contact_info>https://support.google.com/a/answer/2466580</extra_contact_info>
  <errors>
    <error></error>
    <error></error>
    <error></error>
  </errors>
  <source_ip></source_ip>
  <source_dns>
    <dns></dns>
    <dns></dns>
    <dns></dns>
  </source_dns>
  <source_dns_string>domain1, domain2, domain3</source_dns_string>
  <count></count>
  <envelope_to></envelope_to>
  <header_from></header_from>
  <envelope_from></envelope_from>
  <policy_published>
    <domain></domain>
    <adkim></adkim>
    <aspf></aspf>
    <p></p>
    <sp></sp>
    <pct></pct>
    <fo></fo>
  </policy_published>
  <policy_evaluated>
    <disposition></disposition>
    <dkim></dkim>
    <spf></spf>
    <reason>
      <type></type>
      <comment></comment>
    </reason>
    <reason>
      <type></type>
      <comment></comment>
    </reason>
  </policy_evaluated>
  <result_spf>
    <domain></domain>
    <scope></scope>
    <result></result>
  </result_spf>
  <result_dkim>
    <domain></domain>
    <selector></selector>
    <result></result>
    <human_result></human_result>
  </result_dkim>
</syslog_entry>
```

## Config File

See the `config.example.json` for an example.

| Fieldname | Description |
|---|---|
| fetchInterval | How often should the job fetch emails from the IMAP server and process them |
| syslogServer | The syslog server in the format ip:port |
| syslogProtocol | The syslog protocol. can be tcp, udp or "". On empty string the local unix socket is used |
| syslogTag | The syslog tag to add to all messages |
| dnsServer | a custom DNS server to use for queries. Uses the system default if left empty |
| dnsConnectTimeout | timeout when connecting to the DNS server |
| dnsTimeout | timeout when waiting on DNS answers |
| dnsCacheTimeout | how long should DNS answers be cached |
| batchSize | how many emails to fetch per login/logout run. As the IMAP server can simply close connections on timeout and the library can not handle reconnects the mails are fetched in multuple runs |
| imap.host | IMAP server in the format ip:port |
| imap.ssl | use SSL/TLS when connecting to server |
| imap.user | IMAP username |
| imap.pass | IMAP password |
| imap.folder | the IMAP folder the reports are in |
| imap.ignoreCert | Ignore invalid TLS certificates when connecting to the IMAP server |
