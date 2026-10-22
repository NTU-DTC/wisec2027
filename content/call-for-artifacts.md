+++
title = "Call for Artifacts"
hidden = false
[menu.main]
    parent = "Call for"
    weight = 3000
+++
# Call for Artifacts

The organizing committee of the 2027 ACM WiSec Symposium is pleased to offer accepted papers the opportunity to have their research artifacts certified as available, functional, and replicable. This effort continues the effort pioneered in 2017 towards supporting greater reproducibility in mobile and wireless security experimental research. The goal of this process is to increase the impact of mobile and wireless security research, enable dissemination of research results, code and experimental setups, and to enable the research community to build on prior experimental results. We recognize papers whose experimental results were replicated by an independent committee and provide a “badge” in accordance with the terminology defined by ACM.

Authors of accepted papers can participate in this voluntary process by submitting supporting evidence of their experiments’ replicability, following the instructions below. Authors are encouraged to plan ahead when running their experiments, in order to minimize the overhead of applying for these badges.

### Review Process in Brief

The authors upload a VM containing all the necessary data and code required to replicate the results. At least two reviewers are asked to replicate the results for each submission. Authors ensure that submitted VMs are self-contained to the maximum extent possible to eliminate any future version deprecation and backward compatibility issues. The reviewers clarify any issues directly with the authors (and may request updates to the original VM and code).

The submission deadlines for replicability applications are:
- First Cycle: <span color="red">TBD<span>
- Second Cycle:<span color="red">TBD<span>

Authors will be notified if their work was replicated by:
- First Cycle: <span color="red">TBD<span>
- Second Cycle: <span color="red">TBD<span>

The committee awards up to three badges for the public availability of the artifacts, the functionality of the artifacts, and the reproducibility of the results of the publication.

Due to the narrow time window to validate artifacts, authors are strongly encouraged to submit packages early. Submitting early will provide more opportunities for communication and clarification with reviewers if necessary, but will otherwise not prejudice the evaluation. All submissions before the official deadline will be evaluated independently on their own merits.

### Preparing an Artifact Submission

- Prepare a VirtualBox VM with all data/tools installed. It is expected that the authors include within this VM raw data (without any pre-processing) and all the scripts used for pre-processing.
- For each graph/table, provide a directory (Fig_XXX, Table_XXX) which contains a script that enables the committee to regenerate that object.
- Include in the home directory a README file, according to the following format template: [*README.txt*](/artifacts/README.txt). You will also be asked to submit this file separately with the application.
- Provide a link to downloading the VM (e.g. Google Drive or Dropbox)
- Submit the result to the HotCRP [https://wisec26replicability.hotcrp.com/](https://wisec27replicability.hotcrp.com/) \(Need this new link\)

In contrast to last year, we will not automatically make the artifacts available to the public. In order to receive the availablility badge, authors are invited to make their artifacts publicly available and submit the links to them. We encourage to service such as GitHub for code and Zenodo for data.

If you have any questions about the submission process or preparing your work for evaluation, feel free to email the artifact evaluation co-chairs at *wisec26-rep-chairs@googlegroups.com*. We are happy to take these questions and want authors to feel confident that the package they submit is what is needed for a successful evaluation.

<!-- ### Volunteer to Review Submissions

Thanks for your interest! We are currently looking for volunteers.

Artifact evaluation volunteers are typically graduate student researchers, but interested undergraduates or other interested parties are welcome to apply. The main requirements are: 1) the ability to receive a package of software and follow the directions to build and run it; 2) some ability with debugging software you didn’t write; and 3) availability during the two reviewing periods. WiSec 26 accepted paper authors are welcome to participate in this process, though they will not be able to participate in the review of their own submissions. 

**Benefits of reviewing submissions include:**

- The pride and joy of serving the WiSec community and the greater cause of open and replicable science
- Recognition for service on this website (also suitable for mention on a CV/resume – impress your friends!)
- A look “behind the scenes” of the replication process to help you learn what is expected in a replication package and how to improve your own future replication submissions.

*Please send your application to wisec26-rep-chairs@googlegroups.com*-->


### Artifact Evaluation Committee

Ildi Alla, University of Luxembourg, Luxembourg  (Co-Chair) \
Eric Wagner, University of Luxembourg, Luxembourg (Co-Chair)\
Selma Yahia, University of Luxembourg, Luxembourg \
Sotiris Michaelides, RWTH Aachen University, Germany \
Aymen Bouferroum, INRIA Lille - Nord Europe, France \
Lucien Dikla Ngueleo, INRIA Lille - Nord Europe, France \
Luisa Lux, RWTH Aachen University, Germany \
David Heye, RWTH Aachen University, Germany \
Nahom Birhan, Old Dominion University, USA \
Jin Ouyang, Rochester Institute of Technology, USA 
