# api
curl -X GET-H "Authorization: OAuth2 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" -H "Accept: application/vnd.github.v3+json" https://github.com/orgs/myorgpradeem/teams

# To add memeber to a team
curl -L -X PUT -H "Accept: application/vnd.github+json" -H "Authorization: Bearer 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" -H "X-GitHub-Api-Version: 2022-11-28" https://api.github.com/orgs/myorgpradeem/teams/myteam/memberships/pradeem88

# To add comment remotely
curl -X POST -H "Authorization: token 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" -H "Accept: application/vnd.github.v3+json" -d '{"body": "asf"}'   "https://api.github.com/repos/myorgpradeem/mpoctest/issues/157/comments"
