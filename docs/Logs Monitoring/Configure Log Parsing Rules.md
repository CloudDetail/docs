In full log data, you can write logs that meet specific conditions into different log libraries by adding log libraries. At the same time, by configuring log parsing rules, you can structure logs of specific formats and speed up query performance. This document will guide you on how to configure log parsing rules.

## 1. Determine the Application Log Format
Taking Java log format as an example:

```plain
2024-10-17 12:03:18.971 [http-nio-12346-exec-17] INFO  travel.service.TravelServiceImpl - [getRestTicketNumber][Get Rest tickets num][num is: Response(status=1, msg=Get Left Ticket of Internal Success, data=1073741823)]
```

## 2. Write Parsing Rules Based on the Log Format
In APO log parsing rule configuration, the **capture group names** in the regular expression will be extracted as log field columns. Below is an example of a parsing rule that matches the above log format:

```plain
.msg, err = parse_regex(.content, r'(?P<ts>.*?) \[(?P<thread>.*?)\] (?P<level>.*?)  (?P<method>.*?) - (?P<msg>.*)')
if err == null {
 .content = encode_json(.msg)
}
del(.msg)
```

Here, the `parse_regex` part is the regular expression:

```plain
(?P<ts>.*?) \[(?P<thread>.*?)\] (?P<level>.*?)  (?P<method>.*?) - (?P<msg>.*)
```

The above regular expression uses **named capture group syntax** to convert log content into structured tags such as `ts`, `thread`, `level`, `method`, and `msg`. The labels of the named capture groups will appear in the "Log Fields" and correspond to individual columns in the ClickHouse log library, speeding up query performance.

> Note: `ts` and `msg` are two special labels that will not be processed as columns. Please only name "log time" and "log message" as `ts` and `msg`.

### How to Modify the Expression Based on Requirements

Suppose you need to extract `[getRestTicketNumber]` after the `-` symbol in the example log content as a caller method label. You can add `\[?(P<caller>).*?\]` to the regular expression, resulting in the following:

```plain
(?P<ts>.*?) \[(?P<thread>.*?)\] (?P<level>.*?)  (?P<method>.*?) - \[?(P<caller>).*?\](?P<msg>.*)
```

After completing the configuration rule, the capture group format `\[?(P<caller>).*?\]` will extract the log field column `caller`, speeding up filtering and query performance. The structured label `"caller":"getRestTicketNumber"` will then appear in the final log.

> **Extended Explanation: Capture Group Expressions**
> 
> In regular expressions, `(?P<name>...)` is a syntax used for "named capture groups." The `name` within the parentheses is the name of the capture group, allowing you to reference the captured content by name rather than position. **APO uses named capture groups as part of the log parsing rules.**

## 3. Verify the Rule's Correctness
Please enter the log parsing rule in [VRL Playground](https://playground.vrl.dev/) and follow the illustrated process to verify the rule's correctness.

![img-1](/img/Configure%20Log%20Parsing%20Rules%20img-1.png)

## 4. Add to APO Rule Parsing After Successful Verification
![img-2](/img/Configure%20Log%20Parsing%20Rules%20img-2.png)
