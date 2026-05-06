# Agent Skills

A collection of skills for AI coding agents. Skills are packaged instructions and scripts that extend agent capabilities.

Skills follow the [Agent Skills](https://agentskills.io) format.

## Available Skills

### comprehension-review

Generates a self-contained HTML walkthrough, with review questions, for the local changed code. The goal is to help the developer engage with code and to reduce **comprehension debt**.

```
npx skills add https://github.com/danballance/agent-skills --skill comprehension-review
```

Use when:

- You are coding in an agentic style and are fully or partially "out-of-the-loop".
- The changes made are not trivial and you want to improve and test your understanding of the code produced.
- The code changes are still available locally as a diff.

**Please note**: at present this skill only looks at code that hasn't been committed to git. If your agent commits code as it works, then this skill won't work for you right now.

The skill includes a section "AI Writing Tropes to Avoid". This section has been copied directly from this excellent resource:
[tropes.fyi](https://tropes.fyi/tropes-md)

The goal is to try and reduce the number of AI mannerisms and to make the document more agreeable for human consumption.

Please see the below article for an excellent exploration on the topic of **comprehension debt** in agentic programming:
[https://addyosmani.com/blog/comprehension-debt](https://addyosmani.com/blog/comprehension-debt)
