# api
curl -X GET-H "Authorization: OAuth2 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" -H "Accept: application/vnd.github.v3+json" https://github.com/orgs/myorgpradeem/teams

# To add memeber to a team
curl -L -X PUT -H "Accept: application/vnd.github+json" -H "Authorization: Bearer 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" -H "X-GitHub-Api-Version: 2022-11-28" https://api.github.com/orgs/myorgpradeem/teams/myteam/memberships/pradeem88

# To add comment remotely
curl -X POST -H "Authorization: token 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" -H "Accept: application/vnd.github.v3+json" -d '{"body": "asf"}'   "https://api.github.com/repos/myorgpradeem/mpoctest/issues/157/comments"

# name: Add Memeber to org-members

name: Add Memeber to org-members

on:
  issue_comment:
    types: [created]

jobs:
  process-comment:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: GitHub_ID provided in comment 
        run: |
          echo "Provided GitHub_ID is: ${{ github.event.comment.body }}"

      - name: Extract GITHUB_ID
        id: extract
        run: |
          echo "${{ github.event.comment.body }}" > comment.txt
          cat comment.txt  # Check the content of comment.txt
          GITHUB_ID=$(cat comment.txt | cut -d' ' -f2)  # Extract the variable using cut
          echo "Extracted GITHUB_ID: $GITHUB_ID"
          echo "GITHUB_ID=$GITHUB_ID" >> $GITHUB_ENV  # Set the environment variable using Environment Files

      - name: Add Member to org-members team.
        run: |
          # Set organization name, username, and token
          ORG_NAME="myorgpradeem"
          MY_TEAM="myteam"
          USERNAME="$GITHUB_ID"
          TOKEN="${{ secrets.API_TOKEN }}"
          
          # Make request to GitHub API
          response=$(curl -s -o /dev/null -w "%{http_code}" -H "Authorization: token $TOKEN" -H "Accept: application/vnd.github.v3+json" "https://api.github.com/orgs/$ORG_NAME/memberships/$USERNAME")

          # Check Response Status
          if [ $response -eq 200 ]; then
            curl -L -X PUT -H "Accept: application/vnd.github+json" \
              -H "Authorization: Bearer ${{ secrets.API_TOKEN }}" \
              -H "X-GitHub-Api-Version: 2022-11-28" \
              "https://api.github.com/orgs/$ORG_NAME/teams/$MY_TEAM/memberships/$USERNAME"
          elif [ $response -eq 204 ]; then
            echo "$USERNAME is not a member of $ORG_NAME."
          elif [ $response -eq 404 ]; then
            echo "Error: $USERNAME or $ORG_NAME not found."
          else
            echo "Error: HTTP status code $response"
          fi
        env:
          GITHUB_TOKEN: ${{ secrets.API_TOKEN }}
