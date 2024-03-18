# api
curl -X GET-H "Authorization: OAuth2 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" -H "Accept: application/vnd.github.v3+json" https://github.com/orgs/myorgpradeem/teams

# To add memeber to a team
curl -L -X PUT -H "Accept: application/vnd.github+json" -H "Authorization: Bearer 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" -H "X-GitHub-Api-Version: 2022-11-28" https://api.github.com/orgs/myorgpradeem/teams/myteam/memberships/pradeem88

# To add comment remotely
curl -X POST -H "Authorization: token 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" -H "Accept: application/vnd.github.v3+json" -d '{"body": "asf"}'   "https://api.github.com/repos/myorgpradeem/mpoctest/issues/157/comments"

# name: Check Org Private Member

on:
  workflow_dispatch:
    inputs:
      username:
        description: 'Username to check membership for'
        required: true

jobs:
  check_membership:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
      
      - name: Check Org Private Member
        env:
          ORG_NAME: myorgpradeem
          USERNAME: ${{ github.event.inputs.username }}
          PRIVATE_KEY: ${{ secrets.PRIVATE_KEY }}
          APP_ID: 853518
          INSTALLATION_ID: 48299343
        run: |
          JWT=$(echo -n "{ \"iss\": $APP_ID }" | openssl dgst -sha256 -sign <(echo "$PRIVATE_KEY") -binary | openssl enc -base64 -A)
          INSTALLATION_TOKEN=$(curl -s -X POST -H "Authorization: Bearer $JWT" -H "Accept: application/vnd.github.v3+json" \
                               "https://api.github.com/app/installations/$INSTALLATION_ID/access_tokens" \
                               | jq -r .token)
          response=$(curl -s -o /dev/null -w "%{http_code}" -H "Authorization: token $INSTALLATION_TOKEN" \
                      -H "Accept: application/vnd.github.v3+json" \
                      "https://api.github.com/orgs/$ORG_NAME/members/$USERNAME")
          if [ $response -eq 200 ]; then
              echo "$USERNAME is a private member of $ORG_NAME."
          elif [ $response -eq 404 ]; then
              echo "$USERNAME is not a private member of $ORG_NAME."
          else
              echo "Error: HTTP status code $response"
          fi
        shell: bash
Error:


