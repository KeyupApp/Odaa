

    private static final String KEY_URL = "url";

    private static final Implementation IMPLEMENTATION =
            new Implementation(
                    PrivateResourceDetector.class,
                    Scope.JAVA_AND_RESOURCE_FILES,
                    Scope.JAVA_FILE_SCOPE,
                    Scope.RESOURCE_FILE_SCOPE);

    /** The main issue discovered by this detector */
    public static final Issue ISSUE =
            Issue.create(
                    "PrivateResource",
                    "Using private resources",
                    "Private resources should not be referenced; the may not be present everywhere, and "
                            + "even where they are they may disappear without notice.\n"
                            + "\n"
                            + "To fix this, copy the resource into your own project instead.",
                    Category.CORRECTNESS,
                    3,
                    Severity.WARNING,
                    IMPLEMENTATION);

    /** List of resource URLs overriding private resources locally */
    private List<String> overriding;

    /** Constructs a new detector */
    public PrivateResourceDetector() {}

    // ---- implements SourceCodeScanner ----

    @Override
    public boolean appliesToResourceRefs() {
        return true;
    }

    @Override
    public void visitResourceReference(
            @NonNull JavaContext context,
            @NonNull UElement node,
            @NonNull ResourceType resourceType,
            @NonNull String name,
            boolean isFramework) {
        if (!isFramework && isPrivate(context, resourceType, name)) {
            // See if it's a local package reference
            boolean foreignPackage = false;
            boolean globalAnalysis = context.isGlobalAnalysis();
            if (node instanceof UReferenceExpression) {
                PsiElement resolved = ((UReferenceExpression) node).resolve();
                if (resolved instanceof PsiField) {
                    PsiPackage pkg = context.getEvaluator().getPackage(resolved);
                    if (pkg != null) {
                        String pkgName = pkg.getQualifiedName();
                        if (!(pkgName.equals(context.getProject().getPackage())
                                || globalAnalysis
                                        && pkgName.equals(context.getMainProject().getPackage()))) {
                            foreignPackage = true;
                        }
                    }
                }
            }

            // See if this is resource we're overriding locally
            if (!foreignPackage) {
                if (globalAnalysis && referencedInMain(context, resourceType, name)) {
                    return;
                }

                if (overriding != null && overriding.contains(resourceType + ":" + name)) {
                    return;
                }
            }

            String message = createUsageErrorMessage(context, resourceType, name);
            Incident incident = new Incident(ISSUE, node, context.getLocation(node), message);
            if (globalAnalysis) {
                context.report(incident);
            } else {
                context.report(incident, map().put(KEY_URL, "@" + resourceType + "/" + name));
            }
        }
    }

    @Override
    public boolean filterIncident(
            @NonNull Context context, @NonNull Incident incident, @NonNull LintMap map) {
        String urlString = map.getString(KEY_URL, null);
        if (urlString != null) {
            ResourceUrl url = ResourceUrl.parse(urlString);
            return url != null && !referencedInMain(context, url.type, url.name);
        }

        return false;
    }

    private static boolean referencedInMain(
            @NonNull Context context, @NonNull ResourceType resourceType, @NonNull String name) {
        LintClient client = context.getClient();
        Project mainProject = context.getMainProject();
        ResourceRepository repository = client.getResources(mainProject, LOCAL_DEPENDENCIES);
        return repository.hasResources(ResourceNamespace.TODO(), resourceType, name);
    }

    // ---- Implements XmlScanner ----

    @Override
    public Collection<String> getApplicableAttributes() {
        return ALL;
    }

    /** Check resource references: accessing a private resource from an upstream library? */
    @Override
    public void visitAttribute(@NonNull XmlContext context, @NonNull Attr attribute) {
        String value = attribute.getNodeValue();
        ResourceUrl url = ResourceUrl.parse(value);
        if (isPrivate(context, url)) {
            String message = createUsageErrorMessage(context, url.type, url.name);
            context.report(ISSUE, attribute, context.getValueLocation(attribute), message);
        }
    }

    /** Check resource definitions: overriding a private resource from an upstream library? */
    @Override
    public Collection<String> getApplicableElements() {
        return Arrays.asList(
                TAG_STYLE,
                TAG_RESOURCES,
         

[README](https://github.com/markdown-it/markdown-it#markdown-it). 

[API down](https://markdown-it.github.io/markdown-it/)

https://www.w3schools.com/html/html5_svg.asp

https://www.w3schools.com/html/tryit.asp?filename=tryhtml_svg_circle

## keyupApp

[keyup](https://github.com/KeyupApp/Odaa/edit/main/Odaa%20branch)

https://github.com/tadese-nagewo/tadese-nagewo/edit/main/New%20update

https://github.com/1Password/for-open-source/edit/main/README.md

https://github.com/freeCodeCamp/freeCodeCamp

https://github.com/readthedocs/sphinx_rtd_theme
