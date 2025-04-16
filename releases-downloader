#!/bin/bash

# This script downloads all assets from a specific GitHub release.

VERSION="1.0.0"

# Parse command-line arguments using getopt
OPTIONS=$(getopt -o hv --long help,version -- "$@")
if [ $? -ne 0 ]; then
    echo "Error parsing options"
    exit 1
fi

eval set -- "$OPTIONS"

# Process options
while true; do
    case "$1" in
        -h|--help)
            echo "Usage: $0 [options] <repo-link> [<tag-name>]"
            echo "Options:"
            echo "  -h, --help       Show this help message"
            echo "  -v, --version    Show the script version"
            echo "Positional Arguments:"
            echo "  <repo-link>      The GitHub repository link (e.g., https://github.com/owner/repo)"
            echo "  <tag-name>       The tag name of the release (optional, defaults to latest)"
            exit 0
            ;;
        -v|--version)
            echo "$0 version $VERSION"
            exit 0
            ;;
        --)
            shift
            break
            ;;
        *)
            echo "Invalid option: $1"
            exit 1
            ;;
    esac
done

# Handle the repository link as a normal argument
if [ $# -lt 1 ]; then
    echo "Error: Repository link is required."
    echo "Usage: $0 [options] <repo-link> [<tag-name>]"
    echo "Options:"
    echo "  -h, --help       Show this help message"
    echo "  -v, --version    Show the script version"
    echo "Positional Arguments:"
    echo "  <repo-link>      The GitHub repository link (e.g., https://github.com/owner/repo)"
    echo "  <tag-name>       The tag name of the release (optional, defaults to latest)"
    exit 1
fi

REPO_LINK=$1
TAG_NAME=$2
shift 2 # Remove the repo link from the arguments

# Extract owner and repository from the repo link
OWNER=$(echo "$REPO_LINK" | awk -F '/' '{print $(NF-1)}')
REPOSITORY=$(echo "$REPO_LINK" | awk -F '/' '{print $NF}')

# Validate extracted values
if [ -z "$OWNER" ] || [ -z "$REPOSITORY" ]; then
    echo "Error: Invalid repository link format. Expected format: https://github.com/owner/repo"
    exit 1
fi

# If tag name is not provided, fetch the latest release tag
if [ -z "$TAG_NAME" ]; then
    echo "Tag name not provided. Fetching the latest release..."
    TAG_NAME=$(curl -s https://api.github.com/repos/${OWNER}/${REPOSITORY}/releases/latest | jq -r '.tag_name')
    if [ -z "$TAG_NAME" ] || [ "$TAG_NAME" == "null" ]; then
        echo "Error: Unable to fetch the latest release tag."
        exit 1
    fi
    echo "Using the latest release tag: $TAG_NAME"
fi

OUTPUT=$(curl -s https://api.github.com/repos/${OWNER}/${REPOSITORY}/releases/tags/${TAG_NAME}) 
# OUTPUT=$(curl -H "Authorization: token "${ACCESS_TOKEN} https://api.github.com/repos/${OWNER}/${REPOSITORY}/releases/tags/${TAG_NAME}) 

ASSETS_NAME=($(echo ${OUTPUT} | jq '.assets[]."name"'))
ASSETS_ID=($(echo ${OUTPUT} | jq '.assets[]."id"'))

echo "Downloading files from ${OWNER}/${REPOSITORY} with tag ${TAG_NAME}"
echo "Found ${#ASSETS_ID[@]} Files to download"
echo "----------------------------------------"
echo "----------------------------------------"

for i in ${!ASSETS_ID[@]}; do
    echo "----------------------------------------"
    echo "Downloading file number ${i+1}: ${ASSETS_NAME[i]}"
	# Remove the first character
	original_string=${ASSETS_NAME[i]}
	trimmed_string=${original_string:1}

	# Remove the last character
	trimmed_string=${trimmed_string:0:${#trimmed_string}-1}

	# wget --header "Authorization: token "${ACCESS_TOKEN} \
	wget -q \
	 --header "Accept: application/octet-stream" \
	 --header "X-GitHub-Api-Version: 2022-11-28" \
	 https://api.github.com/repos/${OWNER}/${REPOSITORY}/releases/assets/${ASSETS_ID[i]} \
	 -O ${trimmed_string}
    echo "Downloaded successfully"
done
