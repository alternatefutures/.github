# Organization Profile Setup Instructions

Follow these steps to set up your GitHub organization profile.

---

## Step 1: Create the `.github` Repository on GitHub

1. Go to: https://github.com/organizations/alternatefutures/repositories/new
2. **Repository name**: `.github`
3. **Description**: "Organization profile and community health files"
4. **Visibility**: ✅ **Public** (must be public for profile to show)
5. **Initialize**: Don't initialize with README (we'll push our own)
6. Click **"Create repository"**

---

## Step 2: Push the Profile README

From your terminal:

```bash
# Navigate to the .github-org directory
cd /Users/wonderwomancode/Projects/alternatefutures/.github-org

# Initialize git repository
git init

# Add remote (replace with your actual URL if different)
git remote add origin git@github.com:alternatefutures/.github.git

# Create main branch
git checkout -b main

# Add files
git add .

# Commit
git commit -m "feat: add organization profile README

🌐 Add comprehensive organization profile showcasing:
- Core products (CLI, SDK, Web App)
- Repository structure by type
- Quick start guides
- Templates and use cases
- Contributing guidelines
- Community links

🤖 Generated with Claude Code

Co-Authored-By: Claude <noreply@anthropic.com>"

# Push to GitHub
git push -u origin main
```

---

## Step 3: Verify It's Working

1. Wait 1-2 minutes for GitHub to process
2. Visit: https://github.com/alternatefutures
3. You should see your new profile README displayed at the top!

---

## Step 4: Customize (Optional)

### Add a Logo/Banner

1. Create an `assets` or `images` folder in the `.github` repo
2. Upload your organization logo or banner image
3. Update the README to include it:

```markdown
<div align="center">
  <img src="./assets/logo.svg" alt="Alternate Futures" width="600" />
</div>
```

### Pin Important Repositories

1. Go to https://github.com/alternatefutures
2. Click **"Customize your pins"** (near the top)
3. Select up to 6 repositories to pin:
   - ✅ `package-cloud-cli` - Main CLI tool
   - ✅ `package-cloud-sdk` - SDK
   - ✅ `service-cloud-api` - Backend API
   - ✅ `template-cloud-nextjs` - Popular template
   - ✅ `web-app.alternatefutures.ai` - Main app
   - ✅ `repo-cloud-templates` - Templates collection
4. Click **"Save pins"**

### Update Organization Settings

1. Go to: https://github.com/organizations/alternatefutures/settings/profile
2. Update:
   - **Profile picture**: Upload your logo
   - **Display name**: "Alternate Futures"
   - **Description**: "Decentralized infrastructure for the next generation of the web"
   - **URL**: https://alternatefutures.ai
   - **Email**: support@alternatefutures.ai (or your contact email)
   - **Twitter**: @alternatefutures
   - **Location**: (your location)

### Add Repository Topics

Add topics to each repository for better discoverability:

**service-auth**:
`authentication`, `jwt`, `oauth`, `web3`, `depin`, `siwe`, `multi-factor`

**service-cloud-api**:
`graphql`, `api`, `backend`, `postgresql`, `prisma`, `ipfs`, `arweave`

**package-cloud-cli**:
`cli`, `deployment`, `developer-tools`, `ipfs`, `web3`, `depin`, `typescript`

**package-cloud-sdk**:
`sdk`, `javascript`, `typescript`, `api-client`, `web3`, `ipfs`

**Templates**:
`template`, `starter`, `boilerplate`, `nextjs` (or framework name)

---

## Step 5: Additional Community Files (Optional)

You can add more community health files to the `.github` repository:

### CODE_OF_CONDUCT.md
Define community behavior standards.

### CONTRIBUTING.md
Detailed contribution guidelines for all repos.

### SECURITY.md
Security policy and vulnerability reporting.

### SUPPORT.md
How to get help and support.

### FUNDING.yml
Sponsorship/funding links.

These files will automatically apply to all repositories in the organization that don't have their own versions.

---

## Troubleshooting

### Profile not showing?

- **Check visibility**: Repository must be **public**
- **Check file path**: Must be exactly `profile/README.md`
- **Wait**: GitHub can take 1-2 minutes to update
- **Clear cache**: Try in incognito/private browsing mode

### Images not loading?

- Use full GitHub URLs: `https://raw.githubusercontent.com/alternatefutures/.github/main/assets/logo.svg`
- Or use relative paths: `./assets/logo.svg`
- Make sure image files are committed and pushed

### Badges not working?

- Check badge URLs are correct
- Some badges require public repositories or npm packages to be published
- Test badge URLs individually in browser

---

## Next Steps

1. ✅ Create and push the `.github` repository
2. ✅ Verify profile displays correctly
3. ✅ Pin important repositories
4. ✅ Update organization settings
5. ✅ Add topics to repositories
6. ✅ Share your new profile! 🎉

---

## Need Help?

If you run into issues:

1. Check GitHub's docs: https://docs.github.com/en/organizations/collaborating-with-groups-in-organizations/customizing-your-organizations-profile
2. Ask me for help with any specific issues
3. GitHub support: https://support.github.com

---

**Ready to go!** Your organization profile will look professional and welcoming to contributors. 🚀
