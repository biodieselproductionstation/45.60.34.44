# Introduction

The [WSL2-Linux-Kernel][wsl2-kernel] repo contains the kernel source code and
configuration files for the [WSL2][about-wsl2] kernel.

# Reporting Bugs

If you discover an issue relating to WSL or the WSL2 kernel, please report it on
the [WSL GitHub project][wsl-issue]. It is not possible to report issues on the
[WSL2-Linux-Kernel][wsl2-kernel] project.

If you're able to determine that the bug is present in the upstream Linux
kernel, you may want to work directly with the upstream developers. Please note
that there are separate processes for reporting a [normal bug][normal-bug] and
a [security bug][security-bug].

# Feature Requests

Is there a missing feature that you'd like to see? Please request it on the
[WSL GitHub project][wsl-issue].

If you're able and interested in contributing kernel code for your feature
request, we encourage you to [submit the change upstream][submit-patch].

# Build Instructions

Instructions for building an x86_64 WSL2 kernel with an Ubuntu distribution using bash are
as follows:

1. Install the build dependencies:  
   `sudo apt install build-essential flex bison dwarves libssl-dev libelf-dev cpio qemu-utils rsync`

2. Modify WSL2 kernel configs (optional):  
   `make menuconfig KCONFIG_CONFIG=Microsoft/config-wsl`

3. Build the kernel using the WSL2 kernel configuration and put the modules in a `modules`
   folder under the current working directory:  
   `make KCONFIG_CONFIG=Microsoft/config-wsl && make INSTALL_MOD_PATH="$PWD/modules" modules_install`
   
   You may wish to include `-j$(nproc)` on the first `make` command to build in parallel.

4. Install the kernel's UAPI headers into a `headers` folder:  
   `make headers_install INSTALL_HDR_PATH="$PWD/headers"`

5. Build the `perf` tooling into a `perf` folder:  
   `make -C tools/perf NO_JEVENTS=1 NO_JVMTI=1 NO_LIBTRACEEVENT=1 install DESTDIR="$PWD/perf" prefix=/`

Then, you can use a provided script to create a VHDX containing the modules, headers, and perf
tooling:
   `./Microsoft/scripts/gen_artifacts_vhdx.sh "$PWD/modules" "$PWD/headers" "$PWD/perf" $(make -s kernelrelease) modules.vhdx`

To save space, you can now delete the compilation artifacts:
   `make clean && rm -r "$PWD/modules" "$PWD/headers" "$PWD/perf"`

If you prefer, you can also build the VHDX manually as follows. WSL expects the artifacts laid
out under `<kernelrelease>/{modules,linux-headers,perf}`, so first assemble a staging directory:

1. Stage the modules, headers, and perf tooling under the kernel release directory:
   ```
   release=$(make -s kernelrelease)
   mkdir -p "$PWD/staging/$release/linux-headers"
   cp -r "$PWD/modules/lib/modules/$release" "$PWD/staging/$release/modules"
   rm -f "$PWD/staging/$release/modules/build" "$PWD/staging/$release/modules/source"
   cp -r "$PWD/headers/." "$PWD/staging/$release/linux-headers"
   cp -r "$PWD/perf" "$PWD/staging/$release/perf"
   ```

2. Calculate the image size (plus 256MiB for slack):
   `image_size=$(du -bs "$PWD/staging" | awk '{print $1;}'); image_size=$((image_size + (256 * (1<<20))));`

3. Build a populated ext4 image:
   `mke2fs -L '' -d "$PWD/staging" -N $(( $(find "$PWD/staging" | wc -l) + 4096 )) -b 1024 -t ext4 "$PWD/modules.img" $((image_size / 1024))`

4. Convert the img to VHDX:
   `qemu-img convert -O vhdx "$PWD/modules.img" "$PWD/modules.vhdx"`

5. Clean up:
   `rm "$PWD/modules.img" # optionally the $PWD/staging, $PWD/modules, $PWD/headers, and $PWD/perf dirs too`

# Install Instructions

Please see the documentation on the [.wslconfig configuration
file][install-inst] for information on using a custom built kernel.

[wsl2-kernel]:  https://github.com/microsoft/WSL2-Linux-Kernel
[about-wsl2]:   https://docs.microsoft.com/en-us/windows/wsl/about#what-is-wsl-2
[wsl-issue]:    https://github.com/microsoft/WSL/issues/new/choose
[normal-bug]:   https://www.kernel.org/doc/html/latest/admin-guide/bug-hunting.html#reporting-the-bug
[security-bug]: https://www.kernel.org/doc/html/latest/admin-guide/security-bugs.html
[submit-patch]: https://www.kernel.org/doc/html/latest/process/submitting-patches.html
[install-inst]: https://docs.microsoft.com/en-us/windows/wsl/wsl-config#configure-global-options-with-wslconfig

<!-- wp:heading {"level":1,"className":"page-header__title"} -->
<h1 class="wp-block-heading page-header__title">Windows Insider Program</h1>
<!-- /wp:heading -->

<!-- wp:list {"className":"s-content s-list__content s-list__content\u002d\u002dfull"} -->
<ul class="wp-block-list s-content s-list__content s-list__content--full"><!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/08/21/announcing-new-builds-for-21-august-2026/">August 21, 2026Announcing new builds for 21 August 2026</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/08/17/improving-file-explorer-context-menu-faster-simpler-and-more-customizable/">August 17, 2026Improving File Explorer &amp; Context Menu: faster, simpler, and more customizable</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/08/17/announcing-new-builds-for-17-august-2026/">August 17, 2026Announcing new builds for 17 August 2026</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/08/14/announcing-new-release-preview-builds-for-14-august-2026/">August 14, 2026Announcing new Release Preview builds for 14 August 2026</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/07/31/announcing-new-builds-for-31-july-2026/">July 31, 2026Announcing new builds for 31 July 2026</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/07/27/announcing-windows-11-insider-preview-build-29634-1000-for-experimental-future-platforms/">July 27, 2026Announcing Windows 11 Insider Preview Build 29634.1000 for Experimental (Future Platforms)</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/07/21/announcing-windows-11-insider-preview-build-28120-2546-for-experimental-26h1/">July 21, 2026Announcing Windows 11 Insider Preview Build 28120.2546 for Experimental (26H1)</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/07/20/announcing-new-builds-for-20-july-2026/">July 20, 2026Announcing new builds for 20 July 2026</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/07/13/improving-windows-search-box-with-less-clutter-and-more-control/">July 13, 2026Improving Windows Search Box, with less clutter and more control</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/07/06/announcing-new-builds-for-july-6-2026/">July 6, 2026Announcing new builds for July 6 2026</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/06/26/announcing-new-builds-for-26-june-2026-retail-launch-of-new-wip-improvements/">June 26, 2026Announcing new builds for 26 June 2026, retail launch of new WIP improvements</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="c-card__link" href="https://blogs.windows.com/windows-insider/2026/04/24/were-moving-to-experimental-and-beta-announcing-new-builds/">April 24, 2026We’re moving to Experimental and Beta! Announcing new builds for 24 April 2026</a></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:paragraph -->
<p>Load More</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"className":"uhf-footer-nav-group-heading"} -->
<h2 class="wp-block-heading uhf-footer-nav-group-heading">What's new</h2>
<!-- /wp:heading -->

<!-- wp:list {"className":"uhf-footer-nav-group-links"} -->
<ul class="wp-block-list uhf-footer-nav-group-links"><!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/surface/devices/surface-pro">Surface Pro</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/surface/devices/surface-laptop">Surface Laptop</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/surface/devices/surface-laptop-ultra?icid=DSM_Footer_WhatsNew_SurfaceLaptopUltra">Surface Laptop Ultra</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/surface/devices/surface-rtx-spark-dev-box?icid=DSM_Footer_WhatsNew_SurfaceRTXSparkDevBox">Surface RTX Spark Dev Box</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/microsoft-copilot/organizations?icid=DSM_Footer_CopilotOrganizations">Copilot for organizations</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/microsoft-copilot/for-individuals?form=MY02PT&amp;OCID=GE_web_Copilot_Free_868g3t5nj">Copilot for personal use</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/microsoft-products-and-apps">Explore Microsoft products</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/windows/apps-for-windows?icid=DSM_Footer_WhatsNew_Windows11apps">Windows 11 apps</a></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:heading {"className":"uhf-footer-nav-group-heading"} -->
<h2 class="wp-block-heading uhf-footer-nav-group-heading">Microsoft Store</h2>
<!-- /wp:heading -->

<!-- wp:list {"className":"uhf-footer-nav-group-links"} -->
<ul class="wp-block-list uhf-footer-nav-group-links"><!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://account.microsoft.com/">Account profile</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/download">Download Center</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://go.microsoft.com/fwlink/?linkid=2139749">Microsoft Store support</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/store/b/returns">Returns</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/store/b/order-tracking">Order tracking</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/store/b/certified-refurbished-products">Certified Refurbished</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/store/b/why-microsoft-store?icid=footer_why-msft-store_7102020">Microsoft Store Promise</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/store/b/payment-financing-options?icid=footer_financing_vcc">Flexible Payments</a></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:heading {"className":"uhf-footer-nav-group-heading"} -->
<h2 class="wp-block-heading uhf-footer-nav-group-heading">Education</h2>
<!-- /wp:heading -->

<!-- wp:list {"className":"uhf-footer-nav-group-links"} -->
<ul class="wp-block-list uhf-footer-nav-group-links"><!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/education">Microsoft in education</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/education/devices/overview">Devices for education</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/education/products/teams">Microsoft Teams for Education</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/education/products/microsoft-365">Microsoft 365 Education</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/education/how-to-buy">How to buy for your school</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://education.microsoft.com/">Educator training and development</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/store/b/education">Deals for students and parents</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/education/ai-in-education">AI for education</a></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:heading {"className":"uhf-footer-nav-group-heading"} -->
<h2 class="wp-block-heading uhf-footer-nav-group-heading">Business</h2>
<!-- /wp:heading -->

<!-- wp:list {"className":"uhf-footer-nav-group-links"} -->
<ul class="wp-block-list uhf-footer-nav-group-links"><!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/ai?icid=DSM_Footer_AI">Microsoft AI</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/security">Microsoft Security</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/dynamics-365">Dynamics 365</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/microsoft-365/business">Microsoft 365</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/power-platform">Microsoft Power Platform</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/microsoft-teams/group-chat-software">Microsoft Teams</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/microsoft-365-copilot?icid=DSM_Footer_Microsoft365Copilot">Microsoft 365 Copilot</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/store/b/business?icid=CNavBusinessStore">Small Business</a></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:heading {"className":"uhf-footer-nav-group-heading"} -->
<h2 class="wp-block-heading uhf-footer-nav-group-heading">Developer &amp; IT</h2>
<!-- /wp:heading -->

<!-- wp:list {"className":"uhf-footer-nav-group-links"} -->
<ul class="wp-block-list uhf-footer-nav-group-links"><!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://azure.microsoft.com/en-us/">Azure</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://developer.microsoft.com/en-us/">Microsoft Developer</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://learn.microsoft.com/">Microsoft Learn</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/software-development-companies/offers-benefits/isv-success?icid=DSM_Footer_SupportAIMarketplace&amp;ocid=cmm3atxvn98">Support for AI marketplace apps</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://techcommunity.microsoft.com/">Microsoft Tech Community</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://marketplace.microsoft.com/?icid=DSM_Footer_Marketplace&amp;ocid=cmm3atxvn98">Microsoft Marketplace</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/software-development-companies?icid=DSM_Footer_SoftwareCompanies&amp;ocid=cmm3atxvn98">Software companies</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://visualstudio.microsoft.com/">Visual Studio</a></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->

<!-- wp:heading {"className":"uhf-footer-nav-group-heading"} -->
<h2 class="wp-block-heading uhf-footer-nav-group-heading">Company</h2>
<!-- /wp:heading -->

<!-- wp:list {"className":"uhf-footer-nav-group-links"} -->
<ul class="wp-block-list uhf-footer-nav-group-links"><!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://careers.microsoft.com/">Careers</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/about">About Microsoft</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://news.microsoft.com/source/?icid=DSM_Footer_Company_CompanyNews">Company news</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/privacy?icid=DSM_Footer_Company_Privacy">Privacy at Microsoft</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/investor/default.aspx">Investors</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/diversity/default?icid=DSM_Footer_Company_Diversity">Diversity and inclusion</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/accessibility">Accessibility</a></li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li><a class="uhf-footer-link" href="https://www.microsoft.com/en-us/corporate-responsibility/sustainability?icid=DSM_Footer_Sustainability">Sustainability</a></li>
<!-- /wp:list-item --></ul>
<!-- /wp:list -->
