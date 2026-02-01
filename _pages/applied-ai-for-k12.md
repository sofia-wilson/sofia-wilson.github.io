---
layout: archive
title: "Applied AI for K12"
permalink: /applied-ai-for-k12/
author_profile: true
---

**Reproducible Claude Workflow for District Curriculum Adoption Processes**

I serve on a local district Curriculum Adoption Committee. As part of that work, I spent time manually pulling together evidence on high-quality instructional materials (EdReports) and program effectiveness (Evidence for ESSA), and synthesizing what I found into something useful for our committee's decision-making.

Curriculum adoption is already a heavy lift, and digging into considerable research hours to find, distill, and synthesize technical reports or combining multiple sources of evaluation to a cohesive summary adds another layer of work that many districts might not have time or staff capacity for. At the same time, district leaders and educators crave evidence-backed data to guide this important curricular decision.

So I built [this tool](https://claude.ai/public/artifacts/f93cc0ce-2faf-4fa3-818e-0b75f1a2dc5c) as a way to make the process more accessible. It's a reproducible workflow that uses Claude to search EdReports and Evidence for ESSA, synthesize the findings, and generate a report with critical questions to ask vendors—all in a few minutes instead of several hours.

Below I provide instructions on how to copy and paste a prompt into Claude to generate your own report. This is very much a beta project. Feel free to share with district leaders and educators. If you try it out and have feedback, I'd welcome it.

### Resources

- [Curriculum Evaluator Web Tool - Powered By Claude](https://claude.ai/public/artifacts/f93cc0ce-2faf-4fa3-818e-0b75f1a2dc5c)
- [README: How to Use The Math Curriculum Evaluation Tool](/files/README.md)
- [Example Report: CA i-Ready Classroom Mathematics (PDF)](/files/iReady_Classroom_Mathematics_Evaluation_Report.pdf)

### What It Does

1. **Part 1:** Searches EdReports to assess whether instructional materials meet High-Quality Instructional Materials (HQIM) indicators. https://edreports.org/
2. **Part 2:** Searches Evidence for ESSA to evaluate whether rigorous research demonstrates that a curriculum improves student outcomes. https://www.evidenceforessa.org/
3. **Part 3:** Synthethisez findings and generates vendor questions based on evidence gaps

*Note: This is a beta AI tool. Always verify findings using the source URLs provided.*
