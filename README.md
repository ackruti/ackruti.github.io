# Blog
Thank you for visiting my Data Science Blog at [akruti.dev](https://www.akruti.dev/)! This platform is dedicated to sharing insights, tutorials, and discussions about various topics using Data Science and Data Visualization.

## Instructions
### Quick Start
Run the following commands in MacOS terminal to test the blog locally.

```
# Install Hugo (optional)
brew install hugo

# Clone repository
git clone https://github.com/ackruti/ackruti.github.io

# Navigate to the cloned directory
cd ackruti.github.io

# Run the Hugo blog locally
hugo server --logLevel debug --disableFastRender -p 1313
```

Visit the blog at [http://localhost:1313/](http://localhost:1313/)

### Add New Post
```
hugo new content/blog/test_post.md
```

### Add New Bundle Post
```
hugo new --kind page-bundle content/blog/test_bundle_post
```

### Update Theme
To update all submodules in your repository to their latest commits, run the following command in MacOS terminal
```
git submodule update --remote themes/hextra
```