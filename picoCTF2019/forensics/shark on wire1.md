# shark on wire 1 - forensics

## Challenge

Given packet capture and asked to recover flag.

## Solve

Starting analysis, to get an idea of the contents of the packet capture I do `Statistics` --> `Protocol Hierarchy`:

![sow1](https://github.com/user-attachments/assets/fb2978a5-d52a-4d19-8a0a-d3a14e03727f)


We can see that the majority of packets are UDP traffic. So, I filter for UDP traffic and follow UDP stream. In stream 6 we can find the flag:

`picoCTF{StaT31355_636f6e6e}`
