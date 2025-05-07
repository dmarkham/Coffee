# How to Add a New Coffee to Your Tracking System

This document outlines the step-by-step process for adding a new coffee to the personal coffee tracking system.

1.  **Gather Coffee Information**:
    *   Find the product page for the new coffee.
    *   Note down key details such as:
        *   Coffee Name
        *   Roaster Name
        *   Flavor Notes
        *   Roast Level (Light, Medium, Dark, etc.)
        *   Origin (Country, Region - if available)
        *   Processing Method (Washed, Natural, Honey, etc. - if available)
        *   Altitude (if available)
        *   Varietal (if available)
        *   Certifications (Organic, Fair Trade, etc. - if available)
        *   A brief description from the roaster
        *   Package size/weight
        *   The URL of the product page.

2.  **Check for Existing Entry (Optional but Recommended)**:
    *   Quickly search the `bean/` directory to see if an entry for this coffee already exists to avoid duplicates.
        *   *Example Gemini command:* `default_api.grep_search(query="Coffee Name", include_pattern="bean/*.md")`

3.  **Determine the Next Bean File Number**:
    *   Look in the `bean/` directory and find the highest number currently used for a coffee file (e.g., if the last file is `86.Some-Coffee.md`, the next number is 87).
        *   *Example Gemini command:* `default_api.run_terminal_cmd(command="ls -1 bean/ | sort -n | tail -1")` (This shows the last file, from which you derive the next number).

4.  **Create the New Coffee Bean File**:
    *   Create a new markdown file in the `bean/` directory. The naming convention is `XX.Roaster-Name-Coffee-Name.md` (e.g., `87.SuperRoaster-AmazingBlend.md`).
    *   Populate this file with the following structure and information:

        ```markdown
        # Coffee Name - Roaster Name

        ### Description
        Flavor Notes (e.g., Dark Chocolate, Ripe Cherry, Toasted Almond)

        ROAST: [e.g., Medium-Dark]
        ORIGIN: [e.g., Ethiopia, Yirgacheffe]
        PROCESS: [e.g., Washed]
        ALTITUDE: [e.g., 1800-2200 Meters]
        VARIETAL: [e.g., Heirloom Ethiopian Varieties]
        CERTIFICATION: [e.g., USDA Organic]

        [Brief description from the roaster about the coffee. Include any interesting facts or details.]

        [Package size/weight, e.g., 12 oz / 340g]

        ## La Pavoni Settings

        Grind: ?

        Weight: ?


        [Product Page](URL_to_product_page)
        ```
    *   Replace the bracketed placeholders `[]` with the actual information. Leave `Grind: ?` and `Weight: ?` as placeholders to be filled in after you brew the coffee.
        *   *Example Gemini action:* `default_api.edit_file(target_file="bean/XX.New-Coffee.md", code_edit="<content_above>")`

5.  **Update the Container File**:
    *   Decide which container will hold this new coffee (e.g., `container/4.md`).
    *   Open the corresponding container file.
    *   **Important**: Since each container can only hold one coffee, replace any existing coffee link with the new one. If the container was marked as empty, remove the `*Empty - Ready to be filled*` line.
    *   The content should be:

        ```markdown
        # Container X

        [Coffee Name - Roaster Name](../bean/XX.New-Coffee.md)
        ```
        *   *Example Gemini action:* `default_api.edit_file(target_file="container/X.md", code_edit="# Container X\n\n[Coffee Name - Roaster Name](../bean/XX.New-Coffee.md)")`

6.  **Update the README.md File**:
    *   Open the `README.md` file.
    *   Find the table under the "Storage Containers" section.
    *   Update the row for the container you just used to reflect its new content. For example, if you put the new coffee in Container 4:

        ```markdown
        | [Container 4](container/4.md) | Contains [Coffee Name - Roaster Name](bean/XX.New-Coffee.md) |
        ```
    *   Make sure the link points to the correct new bean file.
        *   *Example Gemini action:* `default_api.edit_file(target_file="README.md", code_edit="// ... existing code ...\n| [Container X](container/X.md) | Contains [Coffee Name - Roaster Name](bean/XX.New-Coffee.md) |\n// ... existing code ...")`

7.  **Commit Changes to Git**:
    *   Open your terminal.
    *   Add the new and modified files to the staging area:
        ```bash
        git add bean/XX.New-Coffee.md container/X.md README.md Doc.md
        ```
        (Or use `git add .` to add all changes in the current directory and subdirectories).
    *   Commit the changes with a descriptive message:
        ```bash
        git commit -m "Add [Coffee Name] to Container X and update system (plus documentation)"
        ```
        (Replace `[Coffee Name]` and `X` with specifics).
    *   Push the changes to your remote repository (e.g., GitHub):
        ```bash
        git push origin master
        ```
        (Or just `git push` if your upstream branch is set).

This process ensures that your coffee bean details, container assignments, the main README, and this documentation file are all consistent and up-to-date. When you later brew the coffee and find good La Pavoni settings, you can then edit the specific bean file to record those grind and weight parameters. 