---
id: 33n8xlwmzt3o8eomeve0xnm
title: Gitlabci
desc: ''
updated: 1677781603281
created: 1677781378301
---

## Defect Dojo - CI integration

```
variables:
  DOCKER_HOST: tcp://docker:2375
  DOCKER_DRIVER: overlay2
  DEFECTDOJO_ROOT_URL: "https://defectdojo.internal"
  DEFECTDOJO_PRODUCT_NAME: "${CI_PROJECT_NAME}"
  DEFECTDOJO_SCAN_FORMAT: "application/json"

.publish_scan_results_to_defectdojo:
  image: curl:7.87.0
  before_script: []
  # Scan with no findings will result in a 'failed' run.
  allow_failure: true
  variables:
    # https://gitlab.com/gitlab-org/gitlab/-/issues/4781
    ACCEPT: "accept: application/json"
    CONTENT_TYPE: "content-type: multipart/form-data"
    GIT_STRATEGY: none
    GIT_CHECKOUT: "false"
  script:
    - curl
        --fail-with-body
        --silent
        --noproxy defectdojo.internal
        -u "${DEFECTDOJO_USERNAME}:${DEFECTDOJO_PASSWORD}"
        -H "${ACCEPT}"
        -H "${CONTENT_TYPE}"
        -F 'minimum_severity=Info'
        -F 'active=true'
        -F 'verified=true'
        -F "scan_type=${DEFECTDOJO_SCAN_TYPE}"
        -F "file=@${DEFECTDOJO_SCAN_FILE};type=${DEFECTDOJO_SCAN_FORMAT}"
        -F "product_name=${DEFECTDOJO_PRODUCT_NAME}"
        -F "engagement_name=${DEFECTDOJO_ENGAGEMENT_NAME}"
        -F 'auto_create_context=true'
        -F 'close_old_findings=true'
        -F 'close_old_findings_product_scope=true'
        -F 'push_to_jira=false'
        -F 'group_by=file_path'
        -F 'deduplication_on_engagement=true'
        -F 'create_finding_groups_for_all_findings=true'
        -F "build_id=${CI_PIPELINE_ID}"
        -F "branch_tag=${CI_COMMIT_REF_NAME}"
        -F "commit_hash=${CI_COMMIT_SHA}"
        -F "source_code_management_uri=${CI_PROJECT_URL}/-/blob/${CI_COMMIT_REF_NAME}"
        "${DEFECTDOJO_ROOT_URL}/api/v2/reimport-scan/"

scan_grype:
  image: docker:git@sha256:1f3bdb597458d6a86256b34a0053952c3091adad0f2b0a273b4fda97dabd70ce
  stage: scan
  tags:
    - medium
  before_script:
    - docker info
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - mkdir -p .ast
    - apk update && apk add curl
    - curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh | sh -s -- -b /usr/local/bin
    - curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin
  script:
    - syft gitlab.internal:4567/security/container-construction/$CI_PROJECT_NAME
    - grype gitlab.internal:4567/security/container-construction/$CI_PROJECT_NAME -o json > .ast/grype.json
  artifacts:
    paths:
      - .ast/grype.json

scan_pip-audit:
  image: docker:git@sha256:1f3bdb597458d6a86256b34a0053952c3091adad0f2b0a273b4fda97dabd70ce
  stage: scan
  tags:
    - medium
  before_script:
    - docker info
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - mkdir -p .ast
    - apk update && apk add py3-pip
    - pip3 install pip-audit --ignore-installed
  script:
    - pip-audit -r requirements.txt -f json -o .ast/pip-audit.json || true
  artifacts:
    paths:
      - .ast/pip-audit.json

publish_pip-audit:
  stage: report
  extends: .publish_scan_results_to_defectdojo
  needs:
    - job: scan_pip-audit
      artifacts: true
  variables:
    DEFECTDOJO_SCAN_TYPE: pip-audit Scan
    DEFECTDOJO_SCAN_FILE: .ast/pip-audit.json
    DEFECTDOJO_ENGAGEMENT_NAME: pip-audit - CI

publish_grype_scan:
  stage: report
  extends: .publish_scan_results_to_defectdojo
  needs:
    - job: scan_grype
      artifacts: true
  variables:
    DEFECTDOJO_SCAN_TYPE: Anchore Grype
    DEFECTDOJO_SCAN_FILE: .ast/grype.json
    DEFECTDOJO_ENGAGEMENT_NAME: Grype - CI

```