Follow this multi-step process to create a well-informed commit:

1. Examine repository status
   - Run git status to see all modified, added, and untracked files

2. Stage relevant files
   - Add files explicitely to staging area using git add FILENAME explicitely
   - Only stage files that are part of this logical change

3. Review staged changes
   - Run git diff --cached to see exactly what will be committed

4. Check file sizes
   - Use ls -lh on staged files to view their sizes
   - Identify which files are small enough to read in full

5. Read file contents for context
   - Read files that aren't too long to understand the full context
   - Skip very large files or focus on key sections

6. Write commit message
   - Create a clear, descriptive commit message
   - Format: Brief summary (50 chars), blank line, detailed explanation if needed
   - Focus on what changed and why, not how
