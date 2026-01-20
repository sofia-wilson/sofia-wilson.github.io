# Math Curriculum Quality & Effectiveness Evaluator

A tool for district leaders that generates professional PDF reports synthesizing evidence on instructional materials quality (EdReports) and curriculum effectiveness (Evidence for ESSA).

**Disclaimer:** This is a beta AI tool and automated searches may be prone to error. Always verify findings using the source URLs provided.

---

## How to Use via Claude

### Step 1: Start a Claude Session

Go to [claude.ai](https://claude.ai) and start a new conversation.

### Step 2: Give Claude the Code + Your Request

Copy and paste the entire prompt below into Claude. Before pasting, replace `[CURRICULUM NAME]` (including the brackets) with the curriculum you want to evaluate.

**Example:** Change `[CURRICULUM NAME]` to `Curriculum Associates i-Ready Classroom Mathematics`

**FOR CA DISTRICTS** See list of 2025 State Board of Education Approved Math Instructional Programs here: https://www.cde.ca.gov/ci/ma/im/2025mathpublishers.asp

---

**COPY EVERYTHING BELOW THIS LINE:**

```
Create a file called generate_pdf_report.py with this code, then generate a curriculum evaluation report for [CURRICULUM NAME].

Research the curriculum at:
- EdReports: https://edreports.org
- Evidence for ESSA: https://evidenceforessa.org

Follow these CRITICAL EVALUATION GUIDELINES when writing the report:

SUMMARY GUIDELINES:
- DO NOT overstate findings. Use neutral, precise language.
- EdReports "Meets Expectations" means the curriculum meets the standard—it does NOT mean "exceptional" or "outstanding"
- A "perfect score" means all criteria were met, not exceeded. Write "meets expectations across all gateways" NOT "demonstrates exceptional quality"
- Avoid superlatives: exceptional, outstanding, impressive, remarkable, excellent

ESSA STUDY EVALUATION:
- Note who conducted the study (vendor vs independent third-party)
- Note if the outcome measure is curriculum-aligned (e.g., vendor's own diagnostic assessment) vs independent assessment or standardized state tests
- Flag if no state test or 3rd party, independent assessment results are reported 
- Explain how to make sense of Tier 1 (Strong), Tier 2 (Moderate), Tier 3 (Promising) Evidence for ESSA rating. For example, ESSA Tier 1 evidence typically comes from well-designed and well-implemented randomized controlled trials (RCTs). These studies have minimal threats to validity, including acceptable attrition. Districts can be most confident that the intervention caused the observed positive outcomes. ESSA Tier 2 evidence includes high quality quasi-experimental studies or RCTs with some limitations (e.g., baseline differences, attrition), suggestive of impacts but not meeting gold standard for Tier 1 / RCT. ESSA Tier 3 Evidence includes correlational studies with statistical controls, but not a sufficient signal of causal evidence. If no studies met ESSA inclusion requirements, it means that studies failed to meet the minimum methodological criteria and may lack baseline equivalence, adequate comparison groups, or a qualifying logic model to make rigorous claims of program impact.

STUDY INCLUSION RULES:
- If an ESSA-evaluated study exists, include ONLY the ESSA-evaluated study (do not add vendor-reported studies that weren't ESSA-evaluated)
- If NO ESSA study exists, you may include vendor-reported research but clearly note it is NOT ESSA-validated and why (refer to Tier definitions above).

VENDOR QUESTIONS:
- Questions must be SPECIFIC to the findings from Part 1 (EdReports) and Part 2 (ESSA)
- Probe actual evidence gaps (e.g., curriculum-aligned outcomes, missing grade levels, implementation conditions)
- Example: "Your ESSA-rated study relies on [vendor assessment] as the primary outcome. What evidence supports transfer to independent assessments or state assessments?"

Then generate the PDF.

Here's the code:
```

```python
#!/usr/bin/env python3
"""
Math Curriculum Quality & Effectiveness Evaluator
Generates professional PDF reports for curriculum evaluation.
"""

from reportlab.lib import colors
from reportlab.lib.pagesizes import letter
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.lib.units import inch
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, Table, TableStyle, PageBreak, HRFlowable
from reportlab.lib.enums import TA_LEFT, TA_CENTER, TA_JUSTIFY
from datetime import datetime

COLORS = {
    'primary': colors.HexColor('#1A365D'),
    'secondary': colors.HexColor('#2D3748'),
    'accent': colors.HexColor('#3182CE'),
    'green': colors.HexColor('#38A169'),
    'light_green': colors.HexColor('#C6F6D5'),
    'purple': colors.HexColor('#805AD5'),
    'light_purple': colors.HexColor('#E9D8FD'),
    'orange': colors.HexColor('#DD6B20'),
    'light_orange': colors.HexColor('#FEEBC8'),
    'red': colors.HexColor('#E53E3E'),
    'gray': colors.HexColor('#718096'),
    'light_gray': colors.HexColor('#F7FAFC'),
    'border': colors.HexColor('#E2E8F0'),
    'header_bg': colors.HexColor('#EBF8FF'),
    'callout_bg': colors.HexColor('#F0FFF4'),
}

def get_rating_colors(rating_text, rating_type='edreports'):
    if not rating_text or rating_text == 'N/A':
        return COLORS['light_gray'], COLORS['gray']
    rating_lower = rating_text.lower()
    if rating_type == 'edreports':
        if 'meets expectations' in rating_lower and 'partially' not in rating_lower:
            return COLORS['light_green'], COLORS['green']
        elif 'partially meets' in rating_lower:
            return COLORS['light_orange'], COLORS['orange']
        elif 'does not meet' in rating_lower:
            return colors.HexColor('#FED7D7'), COLORS['red']
        return COLORS['light_gray'], COLORS['gray']
    else:
        if 'strong' in rating_lower or 'tier 1' in rating_lower:
            return COLORS['light_green'], COLORS['green']
        elif 'moderate' in rating_lower or 'tier 2' in rating_lower:
            return COLORS['light_purple'], COLORS['purple']
        elif 'promising' in rating_lower or 'tier 3' in rating_lower:
            return COLORS['light_orange'], COLORS['orange']
        return COLORS['light_gray'], COLORS['gray']

def create_section_header(text, styles):
    style = ParagraphStyle('SectionHeader', parent=styles['Heading2'], fontSize=14,
        textColor=COLORS['primary'], fontName='Helvetica-Bold', spaceBefore=20, spaceAfter=12)
    return Paragraph(text, style)

def create_subsection_header(text, styles):
    style = ParagraphStyle('SubsectionHeader', parent=styles['Heading3'], fontSize=12,
        textColor=COLORS['primary'], fontName='Helvetica-Bold', spaceBefore=14, spaceAfter=8)
    return Paragraph(text, style)

def create_key_value_table(data_pairs, styles):
    table_data = []
    label_style = ParagraphStyle('KVLabel', parent=styles['Normal'], fontSize=10, textColor=COLORS['gray'])
    value_style = ParagraphStyle('KVValue', parent=styles['Normal'], fontSize=10, textColor=COLORS['secondary'], fontName='Helvetica-Bold')
    for label, value, highlight_color in data_pairs:
        label_para = Paragraph(label, label_style)
        if highlight_color:
            colored_style = ParagraphStyle('KVValueColored', parent=value_style, textColor=highlight_color)
            value_para = Paragraph(str(value), colored_style)
        else:
            value_para = Paragraph(str(value), value_style)
        table_data.append([label_para, value_para])
    table = Table(table_data, colWidths=[2.5*inch, 4.5*inch])
    table.setStyle(TableStyle([
        ('BACKGROUND', (0, 0), (0, -1), COLORS['light_gray']),
        ('ALIGN', (0, 0), (-1, -1), 'LEFT'), ('VALIGN', (0, 0), (-1, -1), 'MIDDLE'),
        ('LEFTPADDING', (0, 0), (-1, -1), 12), ('RIGHTPADDING', (0, 0), (-1, -1), 12),
        ('TOPPADDING', (0, 0), (-1, -1), 10), ('BOTTOMPADDING', (0, 0), (-1, -1), 10),
        ('GRID', (0, 0), (-1, -1), 0.5, COLORS['border']),
    ]))
    return table

def create_essa_tier_explainer(styles):
    title_style = ParagraphStyle('ExplainerTitle', parent=styles['Normal'], fontSize=11,
        textColor=COLORS['primary'], fontName='Helvetica-Bold', spaceAfter=8)
    tier_style = ParagraphStyle('TierStyle', parent=styles['Normal'], fontSize=9,
        textColor=COLORS['secondary'], leading=13, spaceAfter=4)
    content = [
        [Paragraph("<b>Understanding ESSA Evidence Tiers</b>", title_style)],
        [Paragraph('<font color="#38A169"><b>Tier 1 (Strong Evidence):</b></font> At least one well-designed RCT showing statistically significant positive effects.', tier_style)],
        [Paragraph('<font color="#805AD5"><b>Tier 2 (Moderate Evidence):</b></font> At least one well-designed quasi-experimental study showing statistically significant positive effects.', tier_style)],
        [Paragraph('<font color="#DD6B20"><b>Tier 3 (Promising Evidence):</b></font> At least one correlational study with statistical controls showing positive effects.', tier_style)],
        [Paragraph('<font color="#718096"><b>Tier 4 (Demonstrates a Rationale):</b></font> Well-defined logic model based on research, with ongoing evaluation efforts.', tier_style)],
    ]
    table = Table(content, colWidths=[6.75*inch])
    table.setStyle(TableStyle([
        ('BACKGROUND', (0, 0), (-1, -1), COLORS['header_bg']),
        ('BOX', (0, 0), (-1, -1), 1, COLORS['accent']),
        ('LEFTPADDING', (0, 0), (-1, -1), 12), ('RIGHTPADDING', (0, 0), (-1, -1), 12),
        ('TOPPADDING', (0, 0), (-1, -1), 10), ('BOTTOMPADDING', (0, -1), (-1, -1), 10),
    ]))
    return table

def create_study_card(study_info, is_essa_vetted, styles):
    bg_color = COLORS['light_green'] if is_essa_vetted else COLORS['light_gray']
    border_color = COLORS['green'] if is_essa_vetted else COLORS['border']
    title_style = ParagraphStyle('StudyTitle', parent=styles['Normal'], fontSize=10,
        textColor=COLORS['primary'], fontName='Helvetica-Bold', spaceAfter=6)
    detail_style = ParagraphStyle('StudyDetail', parent=styles['Normal'], fontSize=9,
        leading=12, textColor=COLORS['secondary'], spaceAfter=4)
    findings_style = ParagraphStyle('StudyFindings', parent=styles['Normal'], fontSize=9,
        leading=13, textColor=COLORS['secondary'], alignment=TA_JUSTIFY)
    content = []
    content.append(Paragraph(f"<b>{study_info.get('citation', 'Unknown Study')}</b>", title_style))
    content.append(Paragraph(f"<b>Design:</b> {study_info.get('design', 'N/A')}&nbsp;&nbsp;&nbsp;&nbsp;<b>Peer Reviewed:</b> {study_info.get('peer_reviewed', 'No')}", detail_style))
    content.append(Paragraph(f"<b>Source:</b> {study_info.get('source', 'Unknown')}", detail_style))
    if study_info.get('findings'):
        content.append(Spacer(1, 4))
        content.append(Paragraph("<b>Main Findings:</b>", detail_style))
        content.append(Paragraph(study_info['findings'], findings_style))
    if study_info.get('url'):
        link_style = ParagraphStyle('StudyLink', parent=styles['Normal'], fontSize=9,
            textColor=colors.HexColor('#3182CE'), spaceBefore=6)
        content.append(Paragraph(f'<link href="{study_info["url"]}"><u>View study →</u></link>', link_style))
    table = Table([[content]], colWidths=[6.75*inch])
    table.setStyle(TableStyle([
        ('BACKGROUND', (0, 0), (-1, -1), bg_color), ('BOX', (0, 0), (-1, -1), 1, border_color),
        ('LEFTPADDING', (0, 0), (-1, -1), 12), ('RIGHTPADDING', (0, 0), (-1, -1), 12),
        ('TOPPADDING', (0, 0), (-1, -1), 10), ('BOTTOMPADDING', (0, 0), (-1, -1), 10),
        ('VALIGN', (0, 0), (-1, -1), 'TOP'),
    ]))
    return table

def create_appendix_section(styles):
    elements = []
    title = ParagraphStyle('AppendixTitle', parent=styles['Heading2'], fontSize=14,
        textColor=COLORS['primary'], fontName='Helvetica-Bold', spaceBefore=20, spaceAfter=12)
    subsection = ParagraphStyle('Subsection', parent=styles['Heading3'], fontSize=12,
        textColor=COLORS['primary'], fontName='Helvetica-Bold', spaceBefore=14, spaceAfter=8)
    body = ParagraphStyle('Body', parent=styles['Normal'], fontSize=10, leading=14,
        textColor=COLORS['secondary'], alignment=TA_JUSTIFY, spaceAfter=8)
    bullet = ParagraphStyle('Bullet', parent=body, leftIndent=20, bulletIndent=10, spaceAfter=6)
    note = ParagraphStyle('Note', parent=body, fontSize=9, textColor=COLORS['gray'])
    elements.append(Paragraph("Appendix: About This Review", title))
    elements.append(Paragraph("How This Tool Works", subsection))
    elements.append(Paragraph("This review assesses existing evidence of math curriculum quality (alignment, coherence, usability) and effectiveness (evidence that it meaningfully drives student achievement outcomes).", body))
    elements.append(Paragraph("<b>• Part 1 - Evidence of HQIM:</b> Searches EdReports to assess whether instructional materials meet High-Quality Instructional Materials (HQIM) indicators. <link href='https://edreports.org/'><u>https://edreports.org/</u></link>", bullet))
    elements.append(Paragraph("<b>• Part 2 - Evidence of Effectiveness:</b> Searches Evidence for ESSA to evaluate whether rigorous research demonstrates that a curriculum improves student outcomes. <link href='https://www.evidenceforessa.org/'><u>https://www.evidenceforessa.org/</u></link>", bullet))
    elements.append(Paragraph("<b>• Together:</b> Ratings tell us whether a curriculum is well designed (EdReports) and whether there is credible evidence it improves student outcomes (ESSA).", bullet))
    elements.append(Spacer(1, 0.1*inch))
    elements.append(Paragraph("<i>Note: Not finding evidence of effectiveness does not necessarily mean a curriculum is ineffective—it may mean rigorous research hasn't been conducted yet. Newer or smaller programs may lack the resources for large-scale RCTs or quasi-experimental studies. Use these findings as one input among many in your decision-making process.</i>", note))
    elements.append(Spacer(1, 0.15*inch))
    elements.append(Paragraph("What This Review Does NOT Assess", subsection))
    elements.append(Paragraph("This review focuses on curriculum materials and research evidence, but does not provide information on:", body))
    elements.append(Paragraph("<b>• Evidence-based instructional practices:</b> The pedagogical strategies and teaching methods that drive rigorous math instruction.", bullet))
    elements.append(Paragraph("<b>• Local context fit:</b> Whether the curriculum aligns with your specific student population, community needs, and district goals.", bullet))
    elements.append(Paragraph("For evidence-based findings on effective math instruction practices, visit the What Works Clearinghouse (WWC) Math page: <link href='https://ies.ed.gov/ncee/wwc/Math/'><u>https://ies.ed.gov/ncee/wwc/Math/</u></link>", body))
    elements.append(Spacer(1, 0.15*inch))
    elements.append(Paragraph("How to Use This Evidence in Your Decision-Making", subsection))
    elements.append(Paragraph("Research evidence is one important input, but curriculum selection should also consider local context. Consider these questions:", body))
    for q in ["Does this curriculum align with your district's instructional vision?",
              "What do teachers report about usability and student engagement?",
              "Does it meet your students' specific needs (ELL support, accessibility)?",
              "What implementation support does the vendor provide?",
              "What is the total cost of ownership?"]:
        elements.append(Paragraph(f"• {q}", bullet))
    return elements

def generate_vendor_questions(essa_rating, has_essa_studies, has_vendor_studies):
    """
    Generate vendor questions. 
    NOTE: Override this function with questions specific to the actual 
    findings from Part 1 and Part 2 of the review.
    """
    questions = []
    if "no evidence" in essa_rating.lower() or "no qualifying" in essa_rating.lower():
        questions.append("What is your timeline for conducting rigorous efficacy research that would qualify for ESSA Tier 1-3 evidence ratings?")
    if has_vendor_studies and not has_essa_studies:
        questions.append("Are there plans for independent, third-party evaluation of effectiveness?")
    questions.append("What implementation conditions are necessary to replicate the results seen in the research?")
    questions.append("What professional development and ongoing support do you provide for successful implementation?")
    questions.append("Can you connect us with similar districts who have implemented this curriculum?")
    return questions[:4]

def create_pdf_report(data, output_path):
    doc = SimpleDocTemplate(output_path, pagesize=letter,
        leftMargin=0.75*inch, rightMargin=0.75*inch, topMargin=0.75*inch, bottomMargin=0.75*inch)
    story = []
    styles = getSampleStyleSheet()
    main_title = ParagraphStyle('MainTitle', parent=styles['Heading1'], fontSize=18,
        textColor=COLORS['primary'], fontName='Helvetica-Bold', spaceAfter=4)
    beta = ParagraphStyle('Beta', parent=styles['Normal'], fontSize=10,
        textColor=COLORS['accent'], fontName='Helvetica-Bold', spaceAfter=8)
    tagline = ParagraphStyle('Tagline', parent=styles['Normal'], fontSize=10,
        textColor=COLORS['secondary'], leading=14, spaceAfter=12)
    disclaimer = ParagraphStyle('Disclaimer', parent=styles['Normal'], fontSize=9,
        textColor=COLORS['gray'], spaceAfter=16)
    eval_title = ParagraphStyle('EvalTitle', parent=styles['Heading2'], fontSize=14,
        textColor=COLORS['primary'], fontName='Helvetica-Bold', spaceBefore=12, spaceAfter=16)
    summary_style = ParagraphStyle('Summary', parent=styles['Normal'], fontSize=10,
        leading=15, textColor=COLORS['secondary'], alignment=TA_JUSTIFY, spaceAfter=8)
    body = ParagraphStyle('Body', parent=styles['Normal'], fontSize=10, leading=14,
        textColor=COLORS['secondary'], spaceAfter=8)
    label = ParagraphStyle('Label', parent=styles['Normal'], fontSize=11,
        textColor=COLORS['primary'], fontName='Helvetica-Bold', spaceBefore=12, spaceAfter=6)
    question = ParagraphStyle('Question', parent=styles['Normal'], fontSize=10,
        leading=14, textColor=COLORS['secondary'], leftIndent=15, spaceAfter=8)
    link = ParagraphStyle('Link', parent=styles['Normal'], fontSize=10,
        textColor=COLORS['accent'], spaceBefore=8)
    story.append(Paragraph("Math Curriculum Quality & Effectiveness Evaluator", main_title))
    story.append(Paragraph("Beta", beta))
    story.append(Paragraph("A tool for educators that synthesizes evidence on instructional materials quality (EdReports) and curriculum effectiveness (Evidence for ESSA) to support evidence-informed curriculum selection.", tagline))
    story.append(Paragraph("<i>Disclaimer: This is a beta AI tool and automated searches may be prone to error. Verify findings using the source URLs provided.</i>", disclaimer))
    story.append(HRFlowable(width="100%", thickness=1, color=COLORS['border'], spaceAfter=12))
    story.append(Paragraph(f"Evaluation Results: {data['vendor_name']}", eval_title))
    story.append(create_subsection_header("Overall Summary", styles))
    summary_box = Table([[Paragraph(data['summary'], summary_style)]], colWidths=[6.75*inch])
    summary_box.setStyle(TableStyle([('BACKGROUND', (0, 0), (-1, -1), COLORS['callout_bg']),
        ('BOX', (0, 0), (-1, -1), 1, COLORS['green']),
        ('LEFTPADDING', (0, 0), (-1, -1), 12), ('RIGHTPADDING', (0, 0), (-1, -1), 12),
        ('TOPPADDING', (0, 0), (-1, -1), 12), ('BOTTOMPADDING', (0, 0), (-1, -1), 12)]))
    story.append(summary_box)
    story.append(Spacer(1, 0.2*inch))
    story.append(create_section_header("Part 1: Evidence of High-Quality Instructional Materials (EdReports)", styles))
    fc_bg, fc_color = get_rating_colors(data.get('focus_coherence', ''), 'edreports')
    rp_bg, rp_color = get_rating_colors(data.get('rigor_practices', ''), 'edreports')
    us_bg, us_color = get_rating_colors(data.get('usability', ''), 'edreports')
    edreports_pairs = [
        ('Evaluated by EdReports?', 'Yes' if data.get('edreports_evaluated') == 'Yes' else 'No', None),
        ('Product Reviewed', data.get('edreports_link', 'Not reviewed'), None),
        ('Grade Levels Reviewed', data.get('grade_levels', 'K-8'), None),
        ('Focus & Coherence', data.get('focus_coherence', 'N/A'), fc_color if data.get('focus_coherence') and data.get('focus_coherence') != 'N/A' else None),
        ('Rigor & Mathematical Practices', data.get('rigor_practices', 'N/A'), rp_color if data.get('rigor_practices') and data.get('rigor_practices') != 'N/A' else None),
        ('Usability', data.get('usability', 'N/A'), us_color if data.get('usability') and data.get('usability') != 'N/A' else None),
    ]
    story.append(create_key_value_table(edreports_pairs, styles))
    if data.get('edreports_evaluated') == 'Yes' and data.get('edreports_url'):
        story.append(Paragraph(f'<link href="{data["edreports_url"]}"><u>View full EdReports review →</u></link>', link))
    story.append(Spacer(1, 0.25*inch))
    story.append(create_section_header("Part 2: Evidence of Curriculum Effectiveness", styles))
    essa_bg, essa_color = get_rating_colors(data.get('essa_rating', ''), 'essa')
    essa_pairs = [
        ('Evaluated by Evidence for ESSA?', 'Yes' if data.get('essa_evaluated') == 'Yes' else 'No', None),
        ('Overall ESSA Rating', data.get('essa_rating', 'N/A'), essa_color if data.get('essa_rating') and data.get('essa_rating') != 'N/A' else None),
    ]
    story.append(create_key_value_table(essa_pairs, styles))
    story.append(Spacer(1, 0.15*inch))
    story.append(create_essa_tier_explainer(styles))
    story.append(Spacer(1, 0.15*inch))
    essa_studies = data.get('research_studies_essa', [])
    vendor_studies = data.get('research_studies_vendor', [])
    if essa_studies:
        story.append(Paragraph("<b>Research Studies (ESSA-Vetted):</b>", label))
        for study in essa_studies:
            story.append(create_study_card(study, True, styles))
            story.append(Spacer(1, 0.1*inch))
    if vendor_studies:
        story.append(Paragraph("<b>Research Studies (Vendor-Reported, Not ESSA-Evaluated):</b>", label))
        for study in vendor_studies:
            story.append(create_study_card(study, False, styles))
            story.append(Spacer(1, 0.1*inch))
    if data.get('essa_url'):
        story.append(Paragraph(f'<link href="{data["essa_url"]}"><u>View Evidence for ESSA report →</u></link>', link))
    if data.get('implementation_notes'):
        story.append(Paragraph("<b>Implementation Notes:</b>", label))
        story.append(Paragraph(data['implementation_notes'], body))
    story.append(Spacer(1, 0.25*inch))
    story.append(create_section_header("Part 3: Questions You May Ask the Vendor Based on Evidence", styles))
    story.append(Paragraph("Based on the evidence findings above, consider asking the vendor these questions:", body))
    questions = generate_vendor_questions(data.get('essa_rating', ''), bool(essa_studies), bool(vendor_studies))
    for q in questions:
        story.append(Paragraph(f"• {q}", question))
    story.append(PageBreak())
    story.extend(create_appendix_section(styles))
    story.append(Spacer(1, 0.3*inch))
    story.append(HRFlowable(width="100%", thickness=0.5, color=COLORS['border'], spaceAfter=8))
    footer = ParagraphStyle('Footer', parent=styles['Normal'], fontSize=8, textColor=COLORS['gray'], alignment=TA_CENTER)
    story.append(Paragraph(f"Report Generated: {datetime.now().strftime('%B %d, %Y')}", footer))
    doc.build(story)
    print(f"PDF report saved to: {output_path}")


if __name__ == "__main__":
    # This will be populated with research data
    pass
```

---

### Step 3: Replace [CURRICULUM NAME]

Before pasting, replace `[CURRICULUM NAME]` (including the brackets) with the curriculum you want to evaluate. For example:

| Replace this... | With this... |
|-----------------|--------------|
| `[CURRICULUM NAME]` | `Curriculum Associates i-Ready Mathematics` |
| `[CURRICULUM NAME]` | `Carnegie Learning California ClearMath K-8` |
| `[CURRICULUM NAME]` | `Eureka Math2 California K-8` |
| `[CURRICULUM NAME]` | `Amplify Desmos Math 6-8` |
| `[CURRICULUM NAME]` | `McGraw Hill Reveal Math K-8` |
| `[CURRICULUM NAME]` | `Illustrative Mathematics 6-8` |

### Step 4: Download Your Report

Claude will:
1. Create the code file
2. Research the curriculum on EdReports and Evidence for ESSA
3. Generate a professional PDF report with critical evaluation
4. Give you the file to download

**Important:** Always verify the information by checking the source URLs in the report. This is a beta tool.

---

## Quick Reference

### ESSA Evidence Tiers

| Tier | Evidence Type | Meaning |
|------|--------------|---------|
| Tier 1 | Strong | At least one well-designed RCT showing statistically significant positive effects |
| Tier 2 | Moderate | At least one well-designed quasi-experimental study showing statistically significant positive effects |
| Tier 3 | Promising | At least one correlational study with statistical controls showing positive effects |
| Tier 4 | Demonstrates a Rationale | Well-defined logic model based on research, with ongoing evaluation efforts |
| No Qualifying Studies | N/A | Reviewed but no studies met ESSA criteria |

### EdReports Ratings

- **Meets Expectations** — Curriculum meets gateway criteria (this is the standard, not exceptional)
- **Partially Meets Expectations** — Meets some but not all criteria
- **Does Not Meet Expectations** — Does not meet criteria

---

## Understanding the Report Output

### What "Meets Expectations" Means

When EdReports rates a curriculum as "Meets Expectations," this indicates the materials meet the established criteria for quality—it does not mean the curriculum is exceptional or outstanding. Even a "perfect score" simply means all criteria were met, not exceeded.

### How to Read ESSA Study Findings

The report critically evaluates each research study. Pay attention to:

1. **Who conducted the study:** Vendor-conducted research may have different incentives than independent third-party research.

2. **Outcome measures:** If the study used the vendor's own assessment (e.g., i-Ready Diagnostic for i-Ready curriculum), results may not transfer to independent third-party assessments or standardized state tests.

3. **Study design:** Quasi-experimental studies (Tier 2) are less rigorous than randomized controlled trials (Tier 1).

4. **What's missing:** If no third-party assessment or state test results are reported, this is a gap worth noting.

### Why Some Curricula Have No ESSA Rating

Not finding evidence of effectiveness does not mean a curriculum is ineffective—it may mean rigorous research hasn't been conducted yet. Newer or smaller programs may lack resources for large-scale studies.

---

## Resources

- **EdReports:** https://edreports.org
- **Evidence for ESSA:** https://evidenceforessa.org
- **What Works Clearinghouse (Math):** https://ies.ed.gov/ncee/wwc/Math/

---

*This tool was created to help district leaders make evidence-informed curriculum decisions. Always verify findings using the source URLs provided.*
