# 🤝 Contributing to Awesome AI Agent Failures

Thank you for your interest in contributing to this project! This repository thrives on community contributions that help us build a comprehensive understanding of AI agent failure modes and their solutions.

## 🎯 How You Can Contribute

### 📝 1. Share Failure Cases
Document real-world failures you've encountered:
- Follow our failure case submission guidelines
- Include reproduction steps when possible
- Anonymize sensitive information

### 🔧 2. Propose Mitigation Strategies
Share solutions and prevention techniques:
- Describe implementation details
- Link to GitHub repositories with working examples
- Reference related academic work where possible

### 📊 3. Contribute Research
Add academic insights and empirical studies:
- Link to relevant papers and studies
- Summarize key findings
- Discuss practical implications
- Suggest future research directions

### 🛠️ 4. Build Tools
Develop diagnostic and monitoring utilities:
- Failure detection tools
- Mitigation frameworks
- Evaluation benchmarks
- Visualization utilities

## 📋 Submission Guidelines

### For Failure Cases
**Add your example directly to the appropriate failure mode file in `docs/failure-modes/`:**

1. **Choose the Right File**: 
   - `tool-hallucination.md` - for tool output errors
   - `response-hallucination.md` - for agent response errors  
   - `goal-misinterpretation.md` - for wrong objective cases
   - `plan-generation.md` - for flawed planning
   - `tool-use.md` - for incorrect tool selection/usage
   - `verification-termination.md` - for completion failures
   - `prompt-injection.md` - for security bypass cases

2. **Include Required Information**:
   - **Scenario**: Context and setup - what was the agent supposed to do?
   - **Failure**: What went wrong and what the agent did
   - **Impact**: Consequences - financial loss, incorrect outputs, etc.
   - **Source**: Link to publicly verifiable documentation (required)

3. **Follow Existing Format**: Use the pattern `### Company/Product Description (Date)` and match the structure of existing examples

### For Mitigation Strategies
**Add to existing failure mode documentation or create new tool documentation:**

1. **For Detection/Mitigation Techniques**: Add to the "Detection and Mitigation Strategies" section of the relevant failure mode file
2. **For Tools**: Create new documentation in `docs/tools/` following the standard structure (Overview, Key Features, Pricing, Additional Resources)
3. **Include Required Information**:
   - Clear description of your approach and why it works
   - Link to working GitHub repository with implementation
   - When the technique is most effective
   - Performance/effectiveness data if available

### For Research & Tools
- **Research**: Add to README Resources section with brief description highlighting practical insights
- **Tools**: Link to external GitHub repositories with comprehensive documentation
- **Ensure**: All external repositories have proper README, usage instructions, and dependencies listed

## 🚀 Getting Started

### 1. Fork the Repository
```bash
git clone https://github.com/vectara/awesome-agent-failures.git
cd awesome-agent-failures
```

### 2. Create a Feature Branch
```bash
git checkout -b feature/your-contribution-name
```

### 3. Make Your Changes
- Add your content following the submission guidelines above
- Update relevant documentation  
- Ensure formatting consistency

### 4. Submit a Pull Request  
- Provide a clear description of your contribution
- Reference any related issues
- Request review from maintainers


## 📐 Style Guidelines

### Documentation
- Use clear, concise language
- Follow Markdown formatting standards
- Include relevant emojis for section headers
- Maintain consistent terminology across documents

### File Naming
- Use kebab-case for filenames (`my-failure-case.md`)
- Include descriptive names that reflect content
- Follow established directory structure
- Add appropriate file extensions

### Technical Requirements

**Markdown Standards:**
- Use standard GitHub Flavored Markdown syntax
- Include proper heading hierarchy (H1 for main title, H2 for sections, etc.)
- Use consistent formatting for code blocks, links, and emphasis
- Ensure proper line spacing (no unnecessary blank lines between sections)

**Link Requirements:**
- All external links must be publicly accessible (no paywalls or login required)
- Links should be stable and unlikely to break (prefer official sources)
- Use descriptive link text, not "click here" or raw URLs
- Test all links before submission

**Content Standards:**
- Write in clear, professional English
- Use consistent terminology matching existing documentation
- Include specific dates, company names, and quantifiable impacts where possible
- Anonymize sensitive information while maintaining educational value

**Required vs. Optional Fields:**
- **Required Information**: All submission guidelines above must be followed
- **Optional**: Additional context, metrics, or analysis that enhances understanding
- **Conditional**: Some fields may be required based on contribution type (e.g., GitHub links for tools)

## 🏷️ Labels and Categories

### Issue Labels
- `failure-case`: New failure mode documentation
- `mitigation`: Solution or prevention strategy
- `research`: Academic contribution or study
- `tool`: Diagnostic or monitoring utility
- `documentation`: Improvements to existing docs
- `bug`: Error in existing content
- `enhancement`: Feature request or improvement

### Content Categories
Organize contributions into appropriate categories:
- **Failure Modes**: Primary taxonomy categories
- **Case Studies**: Real-world failure examples
- **Solutions**: Mitigation and prevention strategies
- **Tools**: Utilities for detection and analysis
- **Research**: Academic papers and empirical studies

## ✅ Quality Standards
- **Failure Cases**: Must have publicly verifiable source and document real-world impact
- **Mitigation Strategies**: Must link to working implementation with clear documentation  
- **Tools/Research**: Must be publicly accessible and directly relevant to AI agent failure modes

## 👥 Community Guidelines

We are committed to fostering an open and welcoming environment. All contributors are expected to maintain professional and respectful behavior in all interactions.

### Respectful Interaction
- Use inclusive and professional language
- Respect diverse perspectives and experiences
- Provide constructive feedback and criticism
- Credit others' contributions appropriately

### Collaborative Approach
- Build on existing work when possible
- Share knowledge and insights freely
- Help newcomers understand contribution process
- Foster learning-oriented discussions

### Ethical Considerations
- Prioritize beneficial applications over harmful ones
- Respect privacy and confidentiality
- Consider broader societal implications
- Promote responsible AI development

## 📞 Support and Communication

### Getting Help
- **GitHub Issues**: [Create an issue](https://github.com/vectara/awesome-agent-failures/issues) for bugs, questions, or suggestions
- **GitHub Discussions**: [Join discussions](https://github.com/vectara/awesome-agent-failures/discussions) for community Q&A
- **Pull Requests**: Submit contributions via [pull requests](https://github.com/vectara/awesome-agent-failures/pulls)

### Maintainer Contact
- **Repository**: [vectara/awesome-agent-failures](https://github.com/vectara/awesome-agent-failures)
- **Issues**: For technical questions, create a GitHub issue
- **Discussions**: For general questions, use GitHub Discussions

## 📄 Legal and Licensing

### License Agreement
By contributing to this project, you agree to license your contributions under the Apache 2.0 License. This ensures:
- Open access for all users
- Freedom to use, modify, and distribute
- Compatibility with commercial applications
- Community ownership of collective knowledge

### Copyright and Attribution
- Contributors retain copyright to their original work
- Project maintains right to use and distribute contributions
- Proper attribution provided in all derivative works
- Credit given to original authors and sources

### Sensitive Information
- Never include proprietary or confidential information
- Anonymize case studies to protect privacy
- Remove personal identifiers and sensitive data
- Consider legal implications of shared information

---

## 🙏 Thank You

Your contributions make this project valuable for the entire AI community. Whether you're sharing a failure case, proposing a solution, or improving documentation, you're helping build a safer and more reliable future for AI agents.

**Ready to contribute?** Check out our [good first issues](https://github.com/vectara/awesome-agent-failures/labels/good%20first%20issue) to get started!

---

*For questions about contributing, please [create an issue](https://github.com/vectara/awesome-agent-failures/issues) or start a [discussion](https://github.com/vectara/awesome-agent-failures/discussions).*