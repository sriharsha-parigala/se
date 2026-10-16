# Maven Web project for Jenkins Week 8 assignment

This is a minimal Maven WAR project for the MavenWeb_Build -> MavenWeb_Test -> MavenWeb_Deploy workflow in the supplied assignment.

## Requirements
- JDK 11+
- Maven configured in Jenkins (for example, global tool name `MAVEN_HOME`)
- Tomcat 9.x if using the assignment's `Tomcat 9.x remote` deployer configuration
- Jenkins plugins: Git, Maven Integration, Copy Artifact, Build Pipeline (view), Deploy to container

## Build locally
From this folder:
```
mvn clean package
```
The WAR will be `target/maven-web.war`.

## Jenkins configuration (matches the assignment)
1. Create `MavenWeb_Build` as a Freestyle project.
   - SCM: this project's Git repository URL, branch `*/main` (or the branch you use).
   - Build steps: Invoke top-level Maven targets `clean`, then `install`.
   - Archive artifacts: `**/*` (recommended WAR-specific pattern is `target/*.war`).
   - Post-build action: Build other projects -> `MavenWeb_Test`, only if stable.
2. Create `MavenWeb_Test`.
   - Enable Delete workspace before build starts.
   - Copy artifacts from `MavenWeb_Build`, stable build, pattern `**/*` (or preferably `target/*.war`).
   - Invoke Maven target `test`.
   - Archive artifacts. Triggering `MavenWeb_Deploy` from this job is optional if you configure it in the assignment.
3. Create `MavenWeb_Deploy`.
   - Enable Delete workspace before build starts.
   - Copy artifacts from `MavenWeb_Test`, stable build, pattern `**/*` (or preferably `target/*.war`).
   - Post-build action: Deploy WAR/EAR to container.
   - WAR/EAR file pattern: `**/*.war`.
   - Context path: `webpath`.
   - Configure a Tomcat 9 remote container URL and credentials in Jenkins. Use your own valid Tomcat manager credentials; do not use the example password from the handout blindly.
4. Create a Build Pipeline view named `MavenWeb_Pipeline` with initial job `MavenWeb_Build`.

## Important notes
- The assignment asks you to use a GitHub repository. To use this project with Jenkins SCM, first create a repository and push this folder to it.
- The supplied handout's sample Tomcat URL uses port 8085 and HTTPS. Use the actual protocol and port configured for your Tomcat instance; default local Tomcat commonly uses HTTP on port 8080.
- This WAR is a simple static page, not a servlet/JSP application. It is sufficient to demonstrate WAR packaging and deployment.
