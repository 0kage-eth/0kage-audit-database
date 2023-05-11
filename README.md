## Database Structure

- All issues are classfied based on protocol category
- Separated issues based on business logic rather than technical issues. Focus is on understanding vulnerability from a business impact perspective (For eg. if `re-entrancy` A could cause loss of collateral v/s re-entrancy B could lead to inflation of reward tokens, focus is on business impact rather than `re-entrancy`)
- Each issue has its own .md file. File name should be index of the issue
- Each issue has following format
  - **Protocol** - name of protocol
  - **Issue** - issue description
  - **Impact** - impact of this isue
  - **Code** - links to actual code blocks
  - **Recommendation** - recommendations proposed for issue
  - **Links** - Links to audit report or git that discusses the issue in more detail
  - **Learning** - Key learnings (and this is important). Idea is to abstract the issue and have a key process/insight/trick to identify similar issues in future
- Eventually, I will open source this and accept PRs from contributors. Idea is to share and help each other

You can reach out to me on twitter @0kage_eth
